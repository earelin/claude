# backend

A Claude Code plugin for reviewing and refactoring backend changes on the current branch
(or a pull request).

- **Agent** — `code-reviewer`, a principal-level reviewer for backend code changes in any
  language or framework. It scopes the diff, reads the project conventions, scores every
  issue for confidence, and reports only high-confidence findings (correctness & business
  logic, architecture & boundaries, data & transactions, concurrency & scaling, security,
  API contract & testing). Read-only — it never edits files.
- **Skill** — `backend-refactoring`, a principal backend engineer that iteratively improves the
  internal structure of the code the current branch (or a PR) adds or modifies over small,
  behaviour-preserving passes. It keeps the public API, data contract, and query semantics
  identical, gates every pass on a known-green safety net, caps at five passes, and prints a
  summary in the terminal.
- **Skill** — `java-unit-test`, guidance for writing JUnit unit tests for Java code: AssertJ
  for assertions, Mockito for test doubles, snake_case test method names, and a preference for
  stubs over mocks.
- **Skill** — `java-integration-test`, guidance for writing Java integration tests that verify
  interactions with external processes: Testcontainers for real dependencies, AssertJ (with
  AssertJ DB for database state), REST-assured for controller endpoints, snake_case test method
  names, kept in a separate integration source set.
- **Skill** — `java-acceptance-test`, guidance for writing Java acceptance tests that run the
  whole application against mocked downstream services, fixed database datasets, and file
  fixtures, covering high-value user scenarios end to end: AssertJ for assertions, REST-assured
  for HTTP APIs, snake_case test method names, kept in a dedicated module or source set.

## Install

```
/plugin marketplace add earelin/claude
/plugin install backend@earelin-plugins
```

## Usage

Ask Claude to "review the backend changes on this branch" (or "review this backend pull
request") to invoke the `code-reviewer` agent, or "refactor the backend code of this branch"
to invoke the `backend-refactoring` skill. Ask Claude to "write unit tests for this Java class" to
invoke the `java-unit-test` skill, "write integration tests for this repository/controller"
to invoke the `java-integration-test` skill, or "write acceptance tests for this user scenario"
to invoke the `java-acceptance-test` skill.

## Structure

```
backend/
├── .claude-plugin/
│   └── plugin.json          # plugin manifest
├── agents/
│   └── code-reviewer.md     # read-only backend reviewer
└── skills/
    ├── backend-refactoring/
    │   └── SKILL.md         # behaviour-preserving backend refactorer
    ├── java-unit-test/
    │   └── SKILL.md         # writing Java unit tests (AssertJ + Mockito)
    ├── java-integration-test/
    │   └── SKILL.md         # writing Java integration tests (Testcontainers, AssertJ DB, REST-assured)
    └── java-acceptance-test/
        └── SKILL.md         # writing Java acceptance tests (whole app, mocked downstreams, AssertJ, REST-assured)
```
