---
name: architecture-review
description: Review a software architecture or design and provide feedback on its quality. Use when the user asks for a review of a system's architecture or design.
---

# Architecture Review skill

Analyze a software architecture or design and provide structured, actionable feedback on its
quality.

Use the criteria below as a checklist. They are independent prompts, not sequential steps —
work through the ones relevant to the design under review (not every question applies to every
architecture).

## Review criteria

### Design & architecture

- Does the overall design meet the requirements?
- Is the design realistic and feasible in terms of the time needed to implement it (especially in the initial phases of development)?
- Are interfaces well suited to deal with both internal and external interactions?
- Are design principles agreed, shared, and cohesive?
- Is the problem statement properly defined?
- Is the design actually solving the issues within the problem statement?
- Is the FURPS+ model (or similar) taken into account?

### Technology & tools

- Are technologies, platforms, languages, libraries, and tools adequate?

### Judgment & experience

- Does the design show signs of serial hammering (repeatedly forcing the same familiar solution onto every problem)?
- Have industry trends and previous experience been thoughtfully considered?

## Producing the review

1. Read the architecture or design thoroughly before judging it.
2. Work through the relevant criteria above, gathering observations for each category.
3. Report findings grouped by the categories above. For each finding, note its
   severity/priority and give a concrete, actionable suggestion for improvement.
4. Close with an overall assessment of the architecture's quality and its readiness to
   proceed.
