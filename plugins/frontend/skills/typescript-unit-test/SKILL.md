---
name: typescript-unit-test
description: Write Vitest unit tests for plain TypeScript functions and classes (not React components), using the expect API, vi.fn/vi.spyOn/vi.mock for test doubles, and a preference for stubs over mocks. Use when the user wants to write, add, or scaffold unit tests for TypeScript functions, classes, or modules.
---

# Write a TypeScript unit test

Write [Vitest](https://vitest.dev) unit tests for **plain TypeScript functions and classes** —
pure logic, services, utilities, domain models. This skill is **not** for React (or any UI)
components; those are covered by rendering-based component tests. Create test doubles with
Vitest's built-in `vi` helpers, and **prefer stubs over mocks**.

## Before you write

1. Read the project's existing tests and config (`vitest.config.ts` / `vite.config.ts`,
   `package.json`, `tsconfig.json`) — the repo's own conventions, Vitest version, and setup
   files win over this skill. Match the file naming already in use (`*.test.ts` vs `*.spec.ts`,
   colocated next to the source vs. a `__tests__` / `test` directory).
2. Confirm the subject is plain TypeScript, not a component. If it renders UI, stop and use the
   project's component-testing approach instead.
3. Identify the unit under test and its collaborators. A collaborator you own and can control
   through a return value is a candidate for a **stub**; only reach for interaction verification
   when the call itself is the behaviour being tested.

## Rules

- **Assertions use Vitest's `expect`.** Use the expressive, value-specific matchers —
  `toBe` (identity / primitives), `toEqual` / `toStrictEqual` (deep equality), `toContain`,
  `toHaveLength`, `toThrow`, `toHaveBeenCalledWith`, and `resolves` / `rejects` for promises.
  Prefer the most specific matcher over a bare `toBe(true)` on a hand-rolled boolean.
- **Import what you use.** Vitest is explicit by default — `import { describe, it, expect, vi,
  beforeEach } from 'vitest'`. (Only rely on globals if the project sets `globals: true` in its
  Vitest config; follow the project.)
- **Name tests by behaviour, not implementation.** Describe the observable outcome —
  `it('returns an empty list when no orders exist')`, `it('throws when the amount is negative')`.
  Group related cases under a `describe(ClassOrFunctionName)`. No `test`-prefixed noise words.
- **Test doubles use `vi`.** Create standalone fakes with `vi.fn()`, replace a method on a real
  object with `vi.spyOn(obj, 'method')`, and mock an entire module with `vi.mock('./path')`
  (hoisted — access the mock through `vi.mocked(...)`). Reset shared state between tests with
  `beforeEach` (or configure `restoreMocks` / `clearMocks` in the project config).
- **Prefer stubs over mocks.** Default to configuring return values —
  `vi.fn().mockReturnValue(...)`, `.mockResolvedValue(...)`, `spy.mockImplementation(...)` — and
  then assert on the unit's **output** (state verification). Only assert on the interaction
  (`expect(dep).toHaveBeenCalledWith(...)`) when the side effect on a collaborator *is* the
  contract and there is no return value to check (e.g. an event was emitted, a record deleted).
  Don't add a `toHaveBeenCalled` that merely restates a stub.
- **One behaviour per test.** Structure each test as Arrange / Act / Assert. Keep a single
  logical assertion focus; split unrelated checks into separate tests.
- **Keep it a unit test and deterministic.** No network, filesystem, real timers, or database —
  substitute collaborators with stubs. Inject clocks, IDs, and randomness (or use
  `vi.useFakeTimers()` / `vi.setSystemTime(...)`) rather than reading `Date.now()` or
  `Math.random()` inside the code under test.

## Format

```ts
import { describe, it, expect, vi, beforeEach } from 'vitest'
import { OrderService } from './order-service'
import type { OrderRepository } from './order-repository'

describe('OrderService', () => {
  let orders: OrderRepository
  let service: OrderService

  beforeEach(() => {
    // stubbed collaborator — only the methods the unit calls
    orders = { findByCustomer: vi.fn() } as unknown as OrderRepository
    service = new OrderService(orders)
  })

  it('returns the total of all orders for a customer', () => {
    // arrange — stub the collaborator's return value
    vi.mocked(orders.findByCustomer).mockReturnValue([{ amount: 10 }, { amount: 15 }])

    // act
    const total = service.totalFor('c-1')

    // assert — on the unit's output
    expect(total).toBe(25)
  })

  it('throws when the customer id is blank', () => {
    expect(() => service.totalFor('')).toThrow(/customer/)
  })

  it('publishes an event when an order is placed', () => {
    // interaction verification is justified: the emitted event is the contract,
    // there is no return value to assert on
    const publish = vi.fn()
    const service = new OrderService(orders, { publish })

    service.place({ amount: 10 })

    expect(publish).toHaveBeenCalledWith(expect.objectContaining({ type: 'order.placed' }))
  })
})
```

## Steps

1. Read the function or class under test and note each collaborator and each observable output
   (return values, thrown errors, resolved/rejected promises, and genuine side effects).
2. Enumerate behaviours to cover: the happy path, boundary and edge cases, and each error /
   rejection path.
3. For each behaviour, write an `it(...)`: arrange by stubbing collaborators, act on the unit,
   and assert the output with `expect`. Reserve `toHaveBeenCalled*` for contracts that are
   purely a side effect. Use `await expect(...).rejects.toThrow(...)` for async failures.
4. Run the tests (`npx vitest run` / the project's test script), confirm they pass, and
   summarise the behaviours covered for the user.
