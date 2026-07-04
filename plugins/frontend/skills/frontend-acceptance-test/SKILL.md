---
name: frontend-acceptance-test
description: Write Playwright acceptance tests that drive the deployed UI as a black box, with the app's external dependencies stubbed by real mock servers (WireMock or similar) brought up via docker-compose so downstream processes are in a predictable state. Use when the user wants to write, add, or scaffold end-to-end / acceptance tests for a web UI, or asks how to test a user journey against a running application.
---

# Write a frontend acceptance test

Write [Playwright](https://playwright.dev) acceptance tests that exercise **high-value user
journeys** against the **running application as a black box** — driven only through the browser,
the way a real user would. The app's **external dependencies are stubbed by real mock servers**
(WireMock or similar) brought up via **docker-compose**, so every downstream process is in a
known, predictable state for each run. For component-level tests use `react-component-test`; for
pure logic use `typescript-unit-test`.

## What this is (and isn't)

- **Black box.** Interact only through the rendered UI — navigate, click, type, read text. Never
  reach into application state, stores, component internals, or the database. If a user can't
  observe it, don't assert on it.
- **Whole system under test.** The UI (and, if present, its backend) runs as it would in
  production. You do **not** stub the app's own code — only the *external* processes it depends
  on (third-party APIs, payment providers, auth, other services).
- **External processes in a predictable state.** Those externals are replaced by mock servers
  (WireMock, Mockoon, or similar) started with the app via docker-compose and loaded with fixed
  stub mappings. Determinism comes from controlling the externals, not from mocking inside the
  browser.
- **Scenario-driven, not exhaustive.** Cover the journeys that matter — sign-up, checkout,
  search-to-result, the critical error path — end to end. Leave field-level and branch coverage
  to unit and component tests.

## Before you write

1. Read the project's existing setup — `playwright.config.ts`, any `docker-compose*.yml`, the
   test directory, and how the app is launched for tests. The repo's own orchestration,
   `baseURL`, and mock tooling win over this skill.
2. Identify the **external dependencies** the running app calls, and confirm each is routed to a
   mock server in the test compose stack (not to the real third party). If one isn't stubbed,
   the test isn't deterministic — add a stub or flag it to the user.
3. Identify the **user journeys** to cover and, for each, the external responses that must be in
   place (happy path, plus the error/edge responses — 404, 500, slow, empty).

## Rules

- **Bring the environment up as real processes.** Use docker-compose to start the app and its
  mock servers (e.g. a `wiremock` service mounting `__files/` and `mappings/`). Point Playwright
  at it with `baseURL`, and orchestrate startup with the config's `webServer` (or a global
  setup) so tests run against a fully-up stack. Seed WireMock via its mappings on disk or its
  `/__admin` API before the journey. Read the app's `baseURL` and the mock server's URL from
  environment variables (not hardcoded hosts) so the same tests run against local
  docker-compose and CI unchanged.
- **Put externals in a known state per scenario.** Prefer static WireMock mappings for the
  default state; for a scenario that needs a specific response, program the stub through the
  admin API in `beforeEach`/`beforeAll` (and reset mappings between tests so scenarios don't
  leak). Each test must be reproducible in isolation.
- **Drive with accessible, user-facing locators.** Prefer `page.getByRole(role, { name })`, then
  `getByLabel`, `getByPlaceholder`, `getByText`. Reserve `getByTestId` for when no accessible
  locator fits. Never select by brittle CSS/XPath tied to structure.
- **Assert with web-first assertions.** Use `await expect(locator).toBeVisible()`,
  `toHaveText`, `toHaveURL`, `toContainText` — they auto-wait and retry. Do not add manual
  `waitForTimeout`/sleeps; let Playwright's auto-waiting handle timing.
- **Assert on observable outcomes.** What the user sees (rendered content, navigation, visible
  success/error state) — and, where it's part of the contract, the request the app made to the
  external, verified via WireMock's `/__admin/requests` (verification API) rather than by
  peeking at internal state.
- **Name tests by the journey and outcome.** `test('checks out successfully with a valid card')`,
  `test('shows a retry message when the catalog service is down')`. Group a journey's steps in
  one `test`; group related journeys in a `test.describe`.
- **Keep tests isolated and stable.** Rely on Playwright's per-test browser-context isolation;
  don't depend on order between tests. Reset external stub state between tests. Test the critical
  paths, not every permutation.

## Format

A test file driving the running UI, with the externals stubbed by a WireMock service in the
compose stack:

```ts
// playwright.config.ts (essentials)
export default defineConfig({
  use: { baseURL: 'http://localhost:8080' },
  // Bring the whole stack up (app + wiremock + deps) before the run.
  webServer: {
    command: 'docker compose -f docker-compose.e2e.yml up --build',
    url: 'http://localhost:8080',
    reuseExistingServer: !process.env.CI,
    timeout: 120_000,
  },
})
```

```yaml
# docker-compose.e2e.yml (essentials)
services:
  app:
    build: .
    ports: ['8080:8080']
    environment:
      CATALOG_API_URL: http://wiremock:8080   # app talks to the mock, not the real service
    depends_on: [wiremock]
  wiremock:
    image: wiremock/wiremock:latest
    ports: ['8081:8080']
    volumes:
      - ./e2e/wiremock/mappings:/home/wiremock/mappings   # default stubs
      - ./e2e/wiremock/__files:/home/wiremock/__files
```

```ts
// e2e/checkout.spec.ts
import { test, expect, request } from '@playwright/test'

// The mock server's admin URL comes from the environment, not a hardcoded host —
// so the same test runs against local docker-compose and CI without edits.
const WIREMOCK = `${process.env.WIREMOCK_URL ?? 'http://localhost:8081'}/__admin`

test.beforeEach(async () => {
  // reset external stub state so each scenario is reproducible in isolation
  const admin = await request.newContext()
  await admin.post(`${WIREMOCK}/mappings/reset`)   // back to the on-disk mappings
})

test.describe('Checkout', () => {
  test('checks out successfully with a valid card', async ({ page }) => {
    // arrange — put the payment external in the "approved" state for this scenario
    const admin = await request.newContext()
    await admin.post(`${WIREMOCK}/mappings`, {
      data: {
        request: { method: 'POST', urlPath: '/payments' },
        response: { status: 200, jsonBody: { status: 'approved', id: 'pay_1' } },
      },
    })

    // act — drive the UI exactly as a user would
    await page.goto('/cart')
    await page.getByRole('button', { name: /checkout/i }).click()
    await page.getByLabel(/card number/i).fill('4242 4242 4242 4242')
    await page.getByRole('button', { name: /pay now/i }).click()

    // assert — on what the user observes
    await expect(page.getByRole('heading', { name: /order confirmed/i })).toBeVisible()
    await expect(page).toHaveURL(/\/orders\/pay_1/)
  })

  test('shows a retry message when the payment service is down', async ({ page }) => {
    const admin = await request.newContext()
    await admin.post(`${WIREMOCK}/mappings`, {
      data: {
        request: { method: 'POST', urlPath: '/payments' },
        response: { status: 500 },
      },
    })

    await page.goto('/cart')
    await page.getByRole('button', { name: /checkout/i }).click()
    await page.getByLabel(/card number/i).fill('4242 4242 4242 4242')
    await page.getByRole('button', { name: /pay now/i }).click()

    await expect(page.getByRole('alert')).toContainText(/couldn.t process your payment/i)
  })
})
```

## Steps

1. Confirm (or set up) the docker-compose stack: the app plus a mock server for every external
   dependency, wired so the app calls the mocks. Point Playwright's `baseURL`/`webServer` at it.
2. Enumerate the user journeys to cover and, for each, the external stub states needed — the
   happy path and the key failure/edge responses.
3. For each journey, load the external state (static mappings or admin API in `beforeEach`),
   drive the UI end to end with accessible locators, and assert on observable outcomes with
   web-first assertions. Reset external state between tests.
4. Run the suite (`npx playwright test` / the project's script) against the compose stack,
   confirm it passes, and summarise the journeys covered and which externals were stubbed.
