# frontend

A Claude Code plugin for reviewing and refactoring frontend changes on the current branch
(or a pull request).

- **Agent** — `code-reviewer`, a principal-level reviewer for frontend code changes in any
  language or framework. It scopes the diff, reads the project conventions, scores every
  issue for confidence, and reports only high-confidence findings (component logic, state &
  data flow, rendering & performance, accessibility, security, UX & contracts). Read-only —
  it never edits files.
- **Skill** — `frontend-refactoring`, a principal frontend engineer that iteratively improves the
  internal structure of the code the current branch (or a PR) adds or modifies over small,
  behaviour-preserving passes. It keeps rendered output, component API, and user-facing
  behaviour identical, gates every pass on a known-green safety net, caps at five passes,
  and prints a summary in the terminal.
- **Skill** — `typescript-unit-test`, guidance for writing Vitest unit tests for plain
  TypeScript functions and classes (not React components): the `expect` API, `vi.fn` /
  `vi.spyOn` / `vi.mock` for test doubles, behaviour-named tests, and a preference for stubs
  over mocks.
- **Skill** — `react-component-test`, guidance for writing Vitest + React Testing Library tests
  that exercise a component's behaviour (interactions, state changes, conditional rendering,
  callbacks) rather than static property rendering: accessible `getByRole` queries, `userEvent`
  interactions, `findBy` / `waitFor` for async, and jest-dom matchers.
- **Skill** — `frontend-acceptance-test`, guidance for writing Playwright acceptance tests that
  drive the deployed UI as a black box, with the app's external dependencies stubbed by real
  mock servers (WireMock or similar) brought up via docker-compose so downstream processes are
  in a predictable state: accessible locators, web-first assertions, and scenario-driven user
  journeys.

## Install

```
/plugin marketplace add earelin/claude
/plugin install frontend@earelin-plugins
```

## Usage

Ask Claude to "review the frontend changes on this branch" (or "review this frontend pull
request") to invoke the `code-reviewer` agent, or "refactor the frontend code of this branch"
to invoke the `frontend-refactoring` skill. Ask Claude to "write unit tests for this TypeScript class"
to invoke the `typescript-unit-test` skill, "write tests for this React component" to invoke
the `react-component-test` skill, or "write acceptance tests for this user journey" to invoke
the `frontend-acceptance-test` skill.

## Structure

```
frontend/
├── .claude-plugin/
│   └── plugin.json          # plugin manifest
├── agents/
│   └── code-reviewer.md     # read-only frontend reviewer
└── skills/
    ├── frontend-refactoring/
    │   └── SKILL.md         # behaviour-preserving frontend refactorer
    ├── typescript-unit-test/
    │   └── SKILL.md         # writing Vitest unit tests (plain TS functions & classes)
    ├── react-component-test/
    │   └── SKILL.md         # writing Vitest + RTL behaviour tests (React components)
    └── frontend-acceptance-test/
        └── SKILL.md         # writing Playwright black-box acceptance tests (WireMock + docker-compose)
```
