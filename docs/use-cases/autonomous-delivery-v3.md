# Autonomous Delivery Use Cases v3

These use cases exercise the role contracts without prescribing a particular orchestration runtime.
Slack is the concise user-decision surface; GitHub Issues and Pull Requests are the durable record.

## Common Invariants

- A Developer starts only from an approved Issue.
- A user decision is never inferred from silence.
- Questions, answers, and resulting decisions are recorded on the Source Issue.
- Every review result and REQUIRED finding is recorded on the Pull Request.
- Only pre-merge gates determine merge readiness.
- A merged PR is never reopened to repair a post-merge E2E failure; recovery uses a follow-up Issue.

## UC-01: Normal autonomous delivery

| Step | Expected outcome |
|---|---|
| User requests work | Product Owner prepares an Issue Preview and asks for explicit approval. |
| User approves | Product Owner creates the approved Source Issue. |
| Developer completes implementation | A non-draft PR with AI Review Handoff and verification evidence is created. |
| Reviewer approves | Full review is recorded; exactly one `agent:merge-ready` state exists. |
| Merger runs | The PR is automatically merged when every Merge Gate passes. |
| Deployer runs | Deployment and post-merge gates are recorded on the merged PR. |

## UC-02: Clarification discovered by any role

| Step | Expected outcome |
|---|---|
| Product Owner, Developer, or Reviewer finds ambiguity | Work pauses and a Product Owner handoff contains the evidence and safe options. |
| Product Owner asks | The user receives a concise question with options `1`, `2`, `3`, and `4`. |
| User replies | `1`/`2` select, `3 <text>` supplies free text, and `4` repeats the question with more context. |
| Decision is known | Question, answer, and decision are recorded on the Source Issue. |
| Scope unchanged | The waiting role resumes from the recorded decision. |
| Scope materially changed | A revised full Issue Preview is approved before the Issue is updated and work resumes. |

## UC-03: Review change request

| Step | Expected outcome |
|---|---|
| Reviewer finds an implementation defect | A REQUIRED finding is recorded on the PR and the state is `agent:changes-1` or `agent:changes-2`. |
| Developer handles it | The same branch and PR receive only the required fix, a disposition comment, and affected verification evidence. |
| New commit is pushed | The reviewer starts exactly one re-review for that new commit. |
| Reviewer approves | The merger, not the reviewer, automatically performs the merge. |

## UC-04: Post-merge E2E failure

| Step | Expected outcome |
|---|---|
| Pre-merge gates pass | Reviewer marks the PR merge-ready even when a gate explicitly marked post-merge has not run. |
| Deployment E2E fails | Deployer records the failed gate and evidence on the merged PR. |
| Follow-up | Product Owner automatically creates a narrowly scoped follow-up Issue linked to the merged PR. |
| Recovery | The follow-up follows UC-01; the original PR remains merged and unchanged. |

## UC-05: Duplicate or delayed delivery events

| Event | Expected outcome |
|---|---|
| Duplicate Slack request, answer, or GitHub webhook | One durable action is taken; later duplicates only observe the existing record. |
| Slack delivery fails after a GitHub mutation | The GitHub record remains authoritative and the concise Slack notification is retried without repeating the mutation. |
| GitHub API temporarily fails | No implementation or review result is fabricated; the existing state is retained and retried. |
| Role worker fails | Its exact failed stage is recorded; it does not advance workflow state. |

## UC-06: Deployment or access blocker

| Step | Expected outcome |
|---|---|
| Deployer cannot safely deploy or verify | It records the failed post-merge gate and evidence as `BLOCKED`. |
| The cause requires a product decision | Product Owner uses UC-02. |
| The cause is an implementation defect | Product Owner creates the narrowly scoped follow-up Issue only when it is a failed post-merge E2E recovery. |
