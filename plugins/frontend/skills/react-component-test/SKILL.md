---
name: react-component-test
description: Write Vitest + React Testing Library tests that exercise a React component's behaviour — user interactions, state changes, conditional rendering, and callbacks — rather than static property rendering. Use when the user wants to write, add, or scaffold tests for a React component, or asks how to test what a component does.
---

# Write a React component test

Write [Vitest](https://vitest.dev) tests for **React components** with
[React Testing Library](https://testing-library.com/docs/react-testing-library/intro). Test the
component's **behaviour** — what a user can do and what they observe as a result — not its
internal implementation or static markup. For plain TypeScript functions and classes, use the
`typescript-unit-test` skill instead.

## Test behaviour, not property rendering

The goal is to assert on what a user experiences, driven by interaction:

- **Do** test: what appears after a click/type/submit, conditional rendering across state,
  callbacks firing with the right arguments, async UI settling (loading → loaded/error),
  disabled/enabled transitions, and accessibility-affecting state (`aria-*`, roles).
- **Don't** write tests that only mirror the props into an assertion (`render(<Badge
  label="New" />)` then `expect(screen.getByText('New')).toBeInTheDocument()` and nothing else).
  A test that just restates the JSX verifies React, not your component. Include such a check only
  as the arrange step of a behaviour, or when conditional-on-props rendering *is* the logic.

## Before you write

1. Read the project's existing tests and config (`vitest.config.ts` / `vite.config.ts`,
   `package.json`, any `setupFiles`) — the repo's conventions, test environment (`jsdom` /
   `happy-dom` / Vitest browser mode), and matcher setup win over this skill. Confirm
   `@testing-library/jest-dom` is imported in a setup file (`import '@testing-library/jest-dom/vitest'`)
   so matchers like `toBeInTheDocument` are available.
2. Identify the component's **observable behaviours**: interactions it responds to, state it
   renders conditionally, callbacks it invokes, and async work it triggers.
3. Identify collaborators to stub — passed-in callback props, injected services, and fetch/API
   modules. Prefer stubbing at the component's boundary (props) over mocking deep internals.

## Rules

- **Query like a user, by accessibility.** Prefer `getByRole(role, { name })`, then
  `getByLabelText`, `getByPlaceholderText`, `getByText`. Reserve `getByTestId` for when no
  accessible query works. Use `getBy*` for elements that must exist now, `queryBy*` to assert
  absence (`expect(screen.queryByRole('alert')).not.toBeInTheDocument()`), and `findBy*`
  (awaited) for elements that appear asynchronously.
- **Drive interactions with `userEvent`, not `fireEvent`.** Call `const user = userEvent.setup()`
  once per test, then `await user.click(...)`, `await user.type(...)`, `await
  user.selectOptions(...)`. `userEvent` models real user behaviour (focus, key events, pointer)
  far more faithfully than `fireEvent`.
- **Assert with jest-dom matchers.** Use `toBeInTheDocument`, `toBeVisible`, `toBeDisabled`,
  `toHaveValue`, `toHaveTextContent`, `toBeChecked`, `toHaveAttribute` — they express intent and
  give better failure messages than poking at DOM properties.
- **Stub callbacks and services with `vi`.** Create handler props with `vi.fn()` and assert
  `expect(onSubmit).toHaveBeenCalledWith(...)`. Mock modules (API clients, `fetch`) with
  `vi.mock('./path')` and drive them through `vi.mocked(...)`. Reset between tests with
  `beforeEach` (or `clearMocks` / `restoreMocks` in config).
- **Handle async explicitly.** `await` every `userEvent` call and every `findBy*`. Wrap
  deferred, non-element assertions in `await waitFor(() => expect(...).toHaveBeenCalled())`.
  Don't add arbitrary `setTimeout`/sleep — let queries retry.
- **Name tests by behaviour.** Describe the user-visible outcome —
  `it('shows an error when submitted with an empty email')`,
  `it('calls onToggle with the new value when the switch is clicked')`. Group under
  `describe(ComponentName)`.
- **One behaviour per test, and keep it isolated.** Arrange (render + stub) / Act (interact) /
  Assert (observe). Rely on React Testing Library's automatic cleanup between tests; don't
  reach into component internals, state, or instance methods.

## Format

```tsx
import { describe, it, expect, vi } from 'vitest'
import { render, screen, waitFor } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { LoginForm } from './login-form'

describe('LoginForm', () => {
  it('submits the entered credentials when the form is valid', async () => {
    // arrange — stub the callback boundary
    const onSubmit = vi.fn()
    render(<LoginForm onSubmit={onSubmit} />)
    const user = userEvent.setup()

    // act — drive it as a user would
    await user.type(screen.getByRole('textbox', { name: /email/i }), 'ada@example.com')
    await user.type(screen.getByLabelText(/password/i), 's3cret')
    await user.click(screen.getByRole('button', { name: /log in/i }))

    // assert — on the observable outcome
    await waitFor(() =>
      expect(onSubmit).toHaveBeenCalledWith({ email: 'ada@example.com', password: 's3cret' }),
    )
  })

  it('shows a validation error and does not submit when the email is empty', async () => {
    const onSubmit = vi.fn()
    render(<LoginForm onSubmit={onSubmit} />)
    const user = userEvent.setup()

    await user.click(screen.getByRole('button', { name: /log in/i }))

    expect(await screen.findByRole('alert')).toHaveTextContent(/email is required/i)
    expect(onSubmit).not.toHaveBeenCalled()
  })

  it('shows the loaded profile after the async request resolves', async () => {
    render(<Profile userId="u-1" />)

    // element is absent initially, appears after the fetch settles
    expect(screen.getByRole('status')).toHaveTextContent(/loading/i)
    expect(await screen.findByRole('heading', { name: /ada lovelace/i })).toBeInTheDocument()
  })
})
```

## Steps

1. Read the component and note its props, the interactions it handles, the state it renders
   conditionally, the callbacks it invokes, and any async work it triggers.
2. Enumerate behaviours to cover: the happy path interaction, validation / error states, empty
   and boundary states, and each callback / async outcome.
3. For each behaviour, write an `it(...)`: render with stubbed props, drive it with `userEvent`,
   and assert on what the user observes (accessible queries + jest-dom matchers). Use `findBy*` /
   `waitFor` for async, and `queryBy*` to assert something is absent.
4. Run the tests (`npx vitest run` / the project's test script), confirm they pass, and
   summarise the behaviours covered for the user.
