# Workflow Labels v3

Workflow labels are durable, machine-readable lifecycle state. Each active Issue or Pull Request
has exactly one primary `agent:*` workflow label. A label is replaced, never accumulated with a
previous primary state. Repository teams may add non-workflow labels separately.

## Issue states

| Label | Meaning | Next owner or transition |
|---|---|---|
| `agent:ready-for-dev` | Approved Issue is ready to start. | Developer → `agent:developing` |
| `agent:developing` | Developer is implementing the approved scope. | PR created, clarification, or blocked |
| `agent:awaiting-clarification` | A Product Owner question needs a user decision. | User decision → prior waiting role |
| `agent:follow-up-e2e` | Automatically created recovery Issue for a failed post-merge E2E gate. | Developer → `agent:developing` |
| `agent:blocked` | A technical, access, or external blocker prevents safe progress. | Explicit recovery action |

## Pull Request states

| Label | Meaning | Next owner or transition |
|---|---|---|
| `agent:review` | A new PR commit is queued for review. | Reviewer |
| `agent:changes-1` | First review cycle has REQUIRED implementation fixes. | Developer |
| `agent:changes-2` | Second review cycle has REQUIRED implementation fixes. | Developer |
| `agent:awaiting-clarification` | Review is waiting for a Product Owner/user decision. | Product Owner |
| `agent:merge-ready` | All pre-merge gates passed. | Merger |
| `agent:merging` | Merge is being executed or confirmed. | Merger → post-merge verification |
| `agent:deploying` | Merged change is being deployed. | Deployer |
| `agent:post-merge-verify` | Deployment is complete and post-merge gates are running. | Deployer |
| `agent:completed` | Deployment and all required post-merge gates succeeded. | Terminal |
| `agent:post-merge-failed` | Deployment or a post-merge E2E gate failed. | Product Owner → `agent:follow-up-e2e` Issue |
| `agent:blocked` | A safe merge, deploy, or verification action cannot proceed. | Explicit recovery action |

## Transition Rules

- A Developer moves an approved Issue to `agent:developing` at actual work start, and creates a
  PR in `agent:review` only after its ready-for-review PR is pushed.
- A clarification pauses the waiting Issue or PR in `agent:awaiting-clarification`; it does not
  consume a review-change cycle or become `agent:blocked`.
- A Developer resolving `agent:changes-*` pushes a new commit and changes the PR to
  `agent:review`; the reviewer never re-reviews the old commit.
- A Reviewer can set only `agent:changes-1`, `agent:changes-2`, `agent:awaiting-clarification`,
  `agent:merge-ready`, or `agent:blocked`.
- A Merger changes `agent:merge-ready` to `agent:merging`, then to `agent:deploying` after merge
  confirmation. It never changes a PR directly to `agent:completed`.
- A Deployer changes `agent:deploying` to `agent:post-merge-verify`, then to `agent:completed`,
  `agent:post-merge-failed`, or `agent:blocked` based on actual evidence.
- A post-merge failure creates a new Issue labelled `agent:follow-up-e2e`; it does not return the
  merged PR to an earlier development or review label.
