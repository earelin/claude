# devops

A Claude Code plugin for reviewing and refactoring DevOps changes — CI/CD pipelines and
infrastructure-as-code — on the current branch (or a pull request).

- **Agent** — `code-reviewer`, a principal-level reviewer for pipeline and infrastructure
  changes in any tool or platform (GitHub Actions, GitLab CI, Jenkins, Argo; Terraform,
  Pulumi, CloudFormation, Kubernetes, Helm, Ansible; AWS, GCP, Azure). It scopes the diff,
  reads the project conventions, scores every issue for confidence, and reports only
  high-confidence findings (pipeline logic, infrastructure correctness & drift, secrets &
  supply chain, security & least privilege, reliability & rollout safety, cost & caching).
  Read-only — it never edits files or applies infrastructure.
- **Agent** — `refactoring`, a principal platform engineer that iteratively improves the
  internal structure of the pipeline and infrastructure code the current branch (or a PR) adds or modifies
  over small, behaviour-preserving passes. It keeps the planned infrastructure, rendered
  output, resource identities, and pipeline graph identical, gates every pass on a known-green
  `validate`/`plan`/`lint` safety net, caps at five passes, and prints a summary in the
  terminal.

## Install

```
/plugin marketplace add earelin/claude
/plugin install devops@earelin-plugins
```

## Usage

Ask Claude to "review the pipeline changes on this branch" (or "review this infrastructure
pull request") to invoke the `code-reviewer` agent, or "refactor the infrastructure code of
this branch" to invoke the `refactoring` agent.

## Structure

```
devops/
├── .claude-plugin/
│   └── plugin.json          # plugin manifest
└── agents/
    ├── code-reviewer.md     # read-only pipeline & infrastructure reviewer
    └── refactoring.md        # behaviour-preserving pipeline & infrastructure refactorer
```
