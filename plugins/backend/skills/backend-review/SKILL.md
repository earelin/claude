---
name: backend-review
description: Review backend pull requests and provide feedback on code changes. Use when the user asks for a review of a pull request or wants to understand the changes in a specific commit.
---

# Backend Review skill

Analyze and provide structured, actionable feedback on backend pull requests and code
changes.

Use the criteria below as a checklist. They are independent prompts, not sequential steps —
work through the ones relevant to the change under review (not every question applies to
every PR).

## Review criteria

### Code structure

- Does the actual implementation reflect the architecture?
- Is the code easy to understand?
- Is the code too long?
- Is cohesion in place?
- Is the code modular?
- Are components cohesive?
- Is the code loosely coupled?
- Is the code reusable?
- Is the code readable?
- Is the code easy to maintain and test?
- Are premature optimizations in place?
- Is composition preferred?
- Is inheritance properly used?
- Is the flow easy to understand?
- Are interactions between different components easy to catch?
- Are conditional flows completely defined?
- Is there any undefined behavior?
- Are APIs consistent and as clean as the overall code?

### Data structures

- Are data structures appropriately used?
- Is the data structure appropriate based on the data size the code is dealing with?
- Are potential changes to data size considered and handled?
- Is the data structured forced to do operations not natively supported?
- Does the data structure support growth (i.e., scalability)?
- Does the data structure reflect the need for relationships between elements?
- Does the data structure optimally support the operations you need to perform on it?
- Is the choice of a specific data structure overcomplicating the code?
- Is the data structure chosen based on most frequent operations to be performed on data?

### Design smells

- Are cyclic dependencies present in the code?
- Is the code feature dense? More than one feature per component is enough to have this smell.
- Are the dependencies stable?
- Does the code have mashed components?
- Are APIs clearly defined?
- Are mesh components present?
- Are first lady components present?
- Are bossy components present?
- Does the class diagram behave like a DAG?
- Does each and every class, method, or function have a single logical responsibility to be achieved?
- Does the class diagram show highly coupled components?
- Is there any function, method, or class with a big LOC number?

### Software architecture

- Are you using design patterns properly?
- Are the patterns the optimal choice based on requirements?
- Are performances taken into account when choosing the design pattern you are inspecting?
- Is the decorator a first lady?
- Does the pattern hinder performance requirements?
- Is your singleton behaving like a first lady?

### Naming & formatting

- Are redundant parameters present in the class (might be the case of instance var instead)?
- Are names of variable, methods, and classes meaningful?
- Are names of variable, methods, and classes descriptive?
- Are names consistent across the codebase?
- Are names context oriented?
- Can you understand from the function/method/class/variable name what is it expected to perform?
- Are too many parameters in place?
- Are optional parameters adequately used?
- Are modifiers used appropriately?
- Are global variables in place? Are they actually needed?
- Does the code contain magic numbers?
- Is abstraction by parameterization achieved?
- Is parameterization needed to remove redundancies?
- Are generic types used when needed to improve reusability?
- Is naming giving insights of a bossy component?

### Comments & documentation

- Are comments coherent with the function/method/class they describe?
- Are comments complete?
- Are pre- and post-conditions properly described?
- Are exceptions and errors documented?
- Are input and output clearly defined and documented?
- Is it clear (or otherwise commented) the type(s) of the input(s)?
- Is it clear (or otherwise commented) the type(s) of the outputs?
- Are all the flows of a method described (including errors/exceptions)?
- Are TODOs, FIX-ME, and similar comments still present in released code?
- Too many inline comments, are they needed?
- Are comments easy to maintain over time?
- Are coding conventions enforced?
- Are obvious comments avoided?
- Are comments and documentation well maintained?
- Are comments used to describe current code only?
- Do comments and documentation contain typos?
- Is the commenting style in line with language guidelines (e.g., PEP8 for Python)?

### Concurrency, parallelism & performance

- Is the code thread safe?
- Are immutable object actually immutable?
- Are race conditions present?
- Is atomicity ensured?
- Are safety and liveness ensured?
- Are deadlocks avoided?
- Is starvation avoided?
- Is fairness ensured?
- Are locking mechanisms properly used?
- Is consistency ensured?
- Is isolation ensured?
- Is durability guaranteed?
- Are you trying to parallelize a problem which is notoriously known as hard to parallelize?
- Are you considering task and data granularity when parallelizing code?
- Are you considering and properly embracing locality needs in your parallelized solution?
- Is load balancing appropriately used? Is the workload uniformly split?
- Are you considering the inherent costs of parallelizing a solution into the overall performances?
- Do you have proper prioritization of performance metrics that are specific to the context your parallel application runs in?

### Security

- Is any sensitive/private/confidential information logged?
- Is any sensitive/private/confidential information disclosed?
- Are audit trails present?
- Are authentication and authorization mechanisms consistently enforced? Are they adequate to the intended security degree wanted?
- Is every access to an object authorized (i.e., complete mediation)?
- Is the least privilege principle enforced?
- Is defense in depth applied?
- Is segregation of duties principle ensured?
- In case of failures, does the system fail safely?
- Is encryption performed? Is it adequate to the security needs?
- Are weak ciphers used?
- Are security keys too small to provide adequate security?
- Are certificates valid?
- Are security keys protected from unauthorized access?
- Are hashing mechanisms used to check for integrity when needed?
- Are security tests in place?
- Which is the weakest link in the security chain? Is it secure enough?
- Are systems secure enough to expose the least attack surface as possible?
- Are all entry points to the system secured?
- Is input validated against well-known attacks (e.g., SQL injection, XSS injection)?
- Does the system plan for failure?
- Is security by obscurity in place instead of proper mechanisms?
- Does the code contain any security bug (also language dependent)?
- Are privacy-enhanced protocols in place when required?
- Are the used protocols tamper resistant?
- Is the code resistant to buffer overflow?
- Is there any hard-coded password?
- Is there any backdoor in the code?
- Is the code compliant with security policies and standards?
- Are security requirements clear?
- Is the team well trained on security?
- Are security response plan and processes in place?

### Testing

- Are the changes covered by unit tests?
- Are integration or contract tests added or updated where behavior crosses a boundary (database, queue, external service)?
- Do tests cover edge cases, error paths, and boundary conditions, not just the happy path?
- Are tests deterministic (no reliance on time, ordering, network, or shared mutable state)?
- Is a regression test added for any bug being fixed?
- Are tests readable and maintainable, with a clear arrange/act/assert structure?

### API & contracts

- Is the public API (REST/GraphQL/gRPC/events) backward compatible, or are breaking changes intentional and versioned?
- Are HTTP status codes, error responses, and payload schemas consistent and correct?
- Are endpoints idempotent where they should be?
- Are request inputs validated and outputs documented?
- Are pagination, filtering, and rate limiting handled for collection endpoints?

### Data & persistence

- Are database migrations safe, reversible, and backward compatible with the running code?
- Are queries efficient (no N+1, appropriate indexes, bounded result sets)?
- Are transactions used to keep related writes atomic and consistent?
- Is data validated and constrained at the persistence layer, not only in code?
- Are large or long-running data operations batched or run asynchronously?

### Error handling & resilience

- Are errors handled explicitly rather than swallowed?
- Are external calls protected with timeouts, retries (with backoff), and circuit breakers where appropriate?
- Does the code fail safely and leave the system in a consistent state?
- Are partial failures and idempotency on retry considered?
- Are user-facing errors distinguished from internal errors, without leaking internals?

### Observability

- Are meaningful logs added at the right levels, without logging sensitive data?
- Are metrics and traces emitted for new or changed critical paths?
- Can the change be debugged in production from its telemetry alone?
- Are alerts or monitoring updated for new failure modes?

### Dependencies

- Is each new dependency necessary and justified over existing options?
- Are dependency versions pinned and free of known vulnerabilities?
- Are the licenses of added dependencies compatible with the project?

### Change scope & compatibility

- Is the PR scoped to a single, coherent change, free of unrelated edits?
- Does the implementation match the PR description and linked requirements?
- Are breaking changes, deployment steps, and feature flags called out?
- Is the change backward compatible with data, clients, and configuration already in production?

## Producing the review

1. Read the pull request or diff (and its description) thoroughly before judging it.
2. Work through the relevant criteria above, gathering observations for each category.
3. Report findings grouped by the categories above. For each finding, note its
   severity/priority and give a concrete, actionable suggestion for improvement.
4. Close with an overall assessment of the change's quality and its readiness to merge.
