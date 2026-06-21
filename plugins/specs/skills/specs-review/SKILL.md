---
name: specs-review
description: Review specifications and provide feedback on their quality. Use when the user asks for a review of a specification or wants to understand the quality of a specific specification.
---

# Specifications Review skill

Analyze a specification and provide structured, actionable feedback on its quality.

Use the criteria below as a checklist. They are independent prompts, not sequential
steps — work through the ones relevant to the specification under review (not every
question applies to every spec).

## Review criteria

### Quality attributes (FURPS+)

- Is usability (end-user perspective) considered?
- Is reliability considered?
- Are performance characteristics described at a high level?
- Is supportability, with all its facets, considered?
- Are operational requirements defined and met?
- Are packaging constraints defined and met? This includes how the code is supposed to be released in production.
- Are legal requirements considered? For example, code released publicly without proper consideration of intellectual property and the processes around it, or possible copyright infringement from reused libraries or data.

### Requirements quality

- Are requirements complete?
- Are they correct?
- Are requirements consistent?
- Is ambiguity removed from them?
- Is the specification realistic?
- Is it verifiable?
- Can requirements be traced back to their related functionalities?

### Process & MVP

- Does the team have the expertise required to advance the project?
- Are the processes in place complete and actually manageable?
- Is the definition of done appropriate?
- Is the minimum viable product (MVP) clearly defined?
- Is the MVP properly defined (i.e., not only functionalities)?

### Team & skills

- Is the team skilled based on the project requirements and technologies?
- Is training needed?
- Does the team have the necessary skills?
- What is the impact of any learning curve on the execution of the project?

### Documentation

- Are findings from the review process properly documented?

## Producing the review

1. Read the specification thoroughly before judging it.
2. Work through the relevant criteria above, gathering observations for each category.
3. Report findings grouped by the categories above. For each finding, note its
   severity/priority and give a concrete, actionable suggestion for improvement.
4. Close with an overall assessment of the specification's quality and its readiness to
   proceed.
