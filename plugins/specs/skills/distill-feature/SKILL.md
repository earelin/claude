---
name: distill-feature
description: Distill a completed FEATURE document into durable system and user documentation — ADRs for decisions, module/architecture docs for current behaviour, user guides for user-visible behaviour, executable specs for rules — verify nothing was lost, then delete the feature and its tasks. Use this whenever a feature is finished, merged, shipped, or implemented and the user wants to close it out, clean up, archive or delete a design doc, update docs after shipping, or asks what to do with finished feature docs — even if they never say the word "distill". Also use when a specs/ or features/ directory is accumulating stale design documents, or when someone asks how to stop maintaining feature docs.
---

# Distill Feature

Turn a finished FEATURE document into permanent documentation, prove nothing was lost, then delete
it along with its tasks.

## The core idea

A FEATURE doc is **scaffolding for a change**. Once the change has shipped, it holds four kinds of
knowledge with four different fates:

| Knowledge | Survives in code? | Fate |
|---|---|---|
| **Why** — decisions, rejected alternatives, constraints | No. Unrecoverable. | → **ADR** (immutable) |
| **What the system now is** — module behaviour, boundaries | Partly, scattered | → **System docs** (living) |
| **What the user can now do** | No | → **User docs** (living) |
| **How it was built** — sequencing, checklists, walkthroughs | Yes, fully | → **Discard** |

Keeping the feature doc afterwards means maintaining a fifth copy that will rot and that an agent
will confidently follow long after it stopped being true. So the end state is removal — but **only
after** the first three rows have provably landed elsewhere.

Two principles govern every judgement here:

1. **Distill the as-built, not the as-designed.** The feature doc is a plan; the merged code is the
   truth. Read both. Where they disagree, the code wins, and the disagreement is itself a finding.
2. **Never restate what an authoritative source already says.** If OpenAPI, a migration, a test, or
   an ArchUnit rule already carries the knowledge, a prose copy is a second source of truth that will
   drift. Point at it; don't duplicate it.

### Tasks are removed too, unconditionally

A task list is entirely row four. Nothing in it is ever distillable, so it gets no plan entry and no
destination — it is deleted at retirement, **including when the feature itself is archived**.
Archiving scaffolding is just the accumulation problem in a different directory.

Two things follow:

- **Find task files explicitly.** They often live outside the feature directory — a `work/tasks/`
  dir, a board export, a `tasks/` subfolder — and will survive if you assume removing the feature
  directory takes them with it.
- **The exception is regulated retention.** If the feature is marked `retention: regulated`, the
  artifact trail may itself be the audit evidence: archive tasks with the feature instead.

The real risk is **design rationale leaking into a task file** — a "note: we capped the jitter window
because…" buried among the checkboxes, about to be destroyed without ever being distilled. Any task
file containing paragraphs rather than checklist items gets read, not just deleted (Phase 0).

## When NOT to run this

Stop and say so — distilling here does damage:

- The feature is **not merged**. Distilling in-flight work writes fiction into permanent docs.
- The feature was **abandoned or reverted**. There is no as-built; mark it and move on.
- The repo has **no ADR practice**. The whole strategy rests on the "why" landing in an immutable
  log. Without one, retiring feature docs loses rationale permanently. Offer to bootstrap ADRs first.
- The feature is marked `retention: regulated` and the user asked for deletion. Offer archive.

## Finding your way around the repo

There is no config file. Work out the layout once, at the start, and say what you found:

```bash
ls -d docs/adr docs/adrs docs/decisions docs/architecture docs/features docs/specs 2>/dev/null
find . -maxdepth 4 -type d \( -name adr -o -name features -o -name specs \) \
  -not -path '*/node_modules/*' -not -path '*/.git/*'
```

Then state it back in one line — "Reading `docs/features/FEAT-0042/`, writing ADRs to `docs/adr/`,
module docs to `docs/architecture/modules/`" — and only ask if something is genuinely ambiguous.
Locate task files in the same pass; they are the ones most likely to be somewhere unexpected.

---

## Phase 0 — Gate

Never distill an unfinished feature. Run these and show the output:

```bash
# status and metadata
head -20 docs/features/FEAT-0042/design.md

# unchecked tasks, across every task file you found
grep -rn '^\s*[-*] \[ \]' docs/features/FEAT-0042/ work/tasks/FEAT-0042* 2>/dev/null

# the merge commit resolves
git rev-parse --verify --quiet <merge_commit>^{commit}

# clean tree, so the distillation commit contains only distillation
git status --porcelain

# ADR practice exists
ls docs/adr/*.md | head
```

Every one of these is a hard stop, not a warning. Do **not** edit front-matter to open a failing
gate — a missing merge commit means the work may not be done.

Two more checks that need judgement rather than a command:

- **Tracker issues closed.** If tasks reference issues (`#101`), confirm they are closed:
  `gh issue view 101 --json state -q .state`. An open task issue means work is still in flight.
- **Prose in task files.** Open any task file that contains paragraphs rather than checkboxes. Task
  files are destroyed without distillation, so anything of substance in them must be pulled into the
  Phase 2 plan or it is gone for good.

## Phase 1 — Inventory the as-built

Read three things, in this order:

1. **The feature doc** — segment it into *knowledge units*: the smallest chunk carrying one idea (a
   paragraph, a table, a bullet cluster, a diagram plus caption). Expect 8–25 on a typical feature.
   Too few means things get lost inside a lump. Include any prose flagged in a task file at Phase 0.
2. **The merged diff** — `git diff <base>..<merge_commit> --stat`, then read the substantive files.
   This is what actually shipped.
3. **The existing docs** — the ADR index, the module docs for every module the diff touched, and the
   user docs. You need to know what is *already* recorded so you don't duplicate it.

Then record **divergences**: places where the doc describes something the code doesn't do, or the
code does something the doc never mentions. A mid-implementation change of approach that nobody wrote
down is usually the most valuable thing in the whole exercise — it is the knowledge most certain to
be lost and most expensive to rediscover.

## Phase 2 — Plan, get approval, write nothing

Classify each unit with this tree. Apply in order; first match wins.

```
1. Scaffolding? — task sequencing, estimates, resolved open questions,
   meeting notes, PR checklists                              → DISCARD (scaffolding)

2. Fully recoverable from the code, with no loss of intent? —
   class listings, method signatures, an algorithm restated   → DISCARD (in code)

3. A DECISION? All four must hold:
   (a) real alternatives existed
   (b) the choice is non-obvious to a competent engineer
   (c) reversing it is expensive
   (d) not already in an ADR                                  → ADR

4. What the system NOW DOES at a module's public boundary —
   inputs, outputs, guarantees, failure modes                 → MODULE DOC

5. Spans modules — auth, transactions, errors, observability,
   idempotency, i18n, caching                                 → CROSSCUTTING DOC

6. A CONTRACT — API shape, event payload, DB schema           → VERIFY OpenAPI / schema / migration

7. A CONSTRAINT enforceable at build time — module boundaries,
   layering, naming                                           → VERIFY ArchUnit / Checkstyle rule

8. A BUSINESS RULE with observable behaviour                  → VERIFY test / Gherkin scenario

9. USER-VISIBLE — something a person can see, do, or must
   understand                                                 → USER DOC

10. Anything else                                             → ASK. Never guess.
```

Steps 6–8 **write no prose at all**. The point is that an executable artifact already carries the
knowledge and cannot silently rot; your job is to confirm it exists and record where. If it doesn't
exist, the only honest moves are write it now, or reclassify to module doc.

Present the plan as a table in chat and **stop**:

| # | Unit | Class | Destination | Why |
|---|---|---|---|---|
| 1 | Backoff chosen over fixed interval | decision | `docs/adr/0042-payment-retry-backoff.md` | Real alternatives, costly to reverse |
| 2 | Scheduler reads with SKIP LOCKED | discard (in code) | — | Restates two methods |
| 3 | payments must not depend on billing | constraint | verify `ModuleBoundaryTest#paymentsMustNotDependOnBilling` | Build-enforced |

This is the trust gate. The end state is deletion, so the human sees what is being kept *before*
anything is written or destroyed. Flag explicitly: any unit you couldn't classify, every divergence,
every task file you had to read, and the **compression estimate** — roughly what fraction of the
feature's prose will survive. If that is above about 30%, you have probably mislabelled discardable
mechanics as system behaviour. Say so and re-check before asking for approval.

For a large feature, write the table to `distill-plan.md` beside the feature so it survives a long
conversation. It gets deleted with the feature in Phase 5.

## Phase 3 — Apply

Write in this order, so a partial failure leaves the most irreplaceable knowledge safe.

**1. ADRs first** — the only unrecoverable content. One decision per ADR; keep them short. If a
decision supersedes an existing ADR, set the old one's status to `Superseded by ADR-NNNN` rather than
editing it. The log is append-only.

```markdown
---
id: ADR-NNNN
title: <the choice made, in one line>
status: accepted
date: YYYY-MM-DD
source_feature: FEAT-NNNN
source_commit: <sha>
---

## Context
The forces at play when this was decided. Written so it stays true — a record of a moment,
not a description of the present.

## Decision
What was chosen, active voice.

## Alternatives considered
- **<Option>** — why rejected, specifically. This is the highest-value section: it stops
  someone re-proposing the same thing in two years.

## Consequences
What gets easier, what gets harder, what this commits us to. Include the costs.
```

**2. System docs** — module docs for behaviour at a module's public boundary, crosscutting docs for
concerns spanning modules. Updated in place, forever. The discipline that keeps them useful: describe
the **boundary, not the internals**. Internals change constantly; boundaries rarely. Cover
responsibility, contracts (pointing at the artifact), guarantees, failure modes, and what the module
deliberately doesn't do.

**3. User docs** — route by what the reader is trying to do, and keep one mode per page:

| Reader's intent | Mode | Shape |
|---|---|---|
| Get me through this task | how-to | numbered steps, assumes competence |
| What are the options / what does this mean | reference | exhaustive, dry, scannable |
| Help me understand why | explanation | prose, context, trade-offs |

Distillation almost always yields reference and how-to. Prefer reference where both would work —
reference stays current, how-tos rot.

**4. Executable claims** — for the `verify` classes, go and check the artifact exists and actually
asserts the thing. Open it; a test whose name matches but whose body asserts something else is worse
than no evidence. Record file and symbol, e.g.
`src/test/java/.../PaymentRetryTest.java#deadLettersAfterThirdFailure`.

**Provenance.** Every file you create or touch gets a marker, so the chain back to the feature
survives its deletion. ADRs use the `source_feature` front-matter above. Living docs get a comment at
the end of the section you touched — invisible when rendered, greppable forever:

```markdown
<!-- distilled-from: FEAT-0042 @ a1b2c3d4 -->
```

## Phase 4 — Verify

Check each of these and report the result:

```bash
# provenance landed everywhere
grep -rl "distilled-from: FEAT-0042" docs/
grep -l "source_feature: FEAT-0042" docs/adr/*.md

# executable evidence actually resolves
grep -n "deadLettersAfterThirdFailure" src/test/java/.../PaymentRetryTest.java

# nothing else still links to the feature path
# (excluding provenance markers, which are supposed to mention it)
grep -rn "FEAT-0042" --include=*.md . \
  | grep -v "docs/features/FEAT-0042" \
  | grep -vE "source_feature:|distilled-from:"
```

Then the **round-trip test**, which is the real safety property and can't be automated:

> Take 5–10 questions the feature doc could answer — "why exponential backoff and not a fixed
> interval?", "what happens when the third retry fails?", "what does the user see when a payment is
> held?" — and answer each using **only** the post-distillation corpus. No peeking at the feature doc.

Any question you can't answer is a hole. Go back to Phase 3 and fill it. Report the questions and
outcomes; this is the evidence that deletion is safe, and it belongs in the commit message.

## Phase 5 — Retire

**1. Write the back-reference into the parent SPEC**, with links relative to the spec file:

```markdown
## Implemented by

- **FEAT-0042** — Payment retry with exponential backoff (retired 2026-08-23, commit `a1b2c3d4`)
  - Decisions: [ADR-0042](../adr/0042-payment-retry-backoff.md)
  - System: [payments module](../architecture/modules/payments.md)
  - User: [Payment states](../user/reference/payment-states.md)
```

**2. Remove the feature and every task file.** Delete is the default; archive when
`retention: regulated`, when an audit trail is required, or for the first few features while the team
builds confidence. Tasks are deleted either way unless retention is regulated.

```bash
git rm -r docs/features/FEAT-0042
git rm work/tasks/FEAT-0042-tasks.md          # task files outside the feature directory
```

If archiving, move the feature instead, stamp its status to `distilled`, delete the task files
anyway, and **add the archive path to `.claudeignore` and your `CLAUDE.md` globs**. An archive an
agent still reads is not an archive.

**3. Commit with this message**, filled in. It is the index that makes the deleted file findable:

```
docs(distill): retire FEAT-0042 after distillation

Distilled into:
  docs/adr/0042-payment-retry-backoff.md   (decision)
  docs/architecture/modules/payments.md    (system behaviour)
  docs/user/reference/payment-states.md    (user behaviour)

Verified against executable artifacts: 2 unit(s)
Discarded: 9 units (6 recoverable from code, 3 scaffolding)
Tasks:     2 file(s) removed, 14 completed items
Round-trip: 7/7 answerable
Original:  docs/features/FEAT-0042/design.md @ a1b2c3d4
```

---

## Refuse to retire if

Each of these means knowledge is about to be lost. Report the reason and stop:

- a unit is still unclassified
- a decision has no ADR
- a `verify` unit's evidence doesn't resolve to a real file and symbol
- a round-trip question is unanswerable from the distilled corpus
- a divergence between doc and code is unresolved
- a task file held prose that never made it into the plan
- `retention: regulated` and the user asked for deletion — offer archive instead

## Getting it back

Deletion is safe because four independent paths survive it: the SPEC's forward links, the provenance
markers pointing back, the commit message index, and git itself.

```bash
git log --diff-filter=D --oneline -- 'docs/features/FEAT-0042/*'
git show <sha>^:docs/features/FEAT-0042/design.md
```

Say this plainly when someone asks "what if we need it later?", along with what is genuinely lost:
the narrative texture of how the team thought about the problem, and the chance of stumbling on the
doc while browsing. Against that: a corpus small enough to stay true, and agents that can't follow a
design that stopped being real.
