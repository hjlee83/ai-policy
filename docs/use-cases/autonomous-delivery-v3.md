# Autonomous Delivery Use Cases v3

These use cases exercise the role contracts without prescribing a particular runtime. Slack is the
concise decision surface; GitHub Issues and Pull Requests are the durable record. Labels describe
the current action and use `review:round-*` or `followup:*` only as supplementary context.

## Common Invariants

- A new Developer starts only from an approved Issue labelled `develop:ready`.
- A resume Developer works in the existing Issue worktree or PR branch; it never creates a second
  PR for the same Source Issue.
- A user decision is never inferred from silence. Questions, answers, and decisions are recorded
  on the Source Issue.
- Every review result and REQUIRED finding is recorded on the Pull Request.
- Only pre-merge gates determine `merge:ready`.
- A merged PR is never reopened to repair a post-merge failure; recovery uses a follow-up Issue.

## UC-01: Normal autonomous delivery

```text
Slack request
  -> Issue [develop:ready]
  -> Developer claim [develop:working]
  -> PR created [review:ready]
  -> Reviewer claim [review:working]
  -> approved [merge:ready]
  -> automatic merge [merge:working]
  -> deployment [deploy:working]
  -> post-merge E2E [e2e:working]
  -> complete [work:done]
```

## UC-02: Developer clarification

```text
Issue [develop:working]
  -> ambiguity [develop:clarify]
  -> Slack question: 1 option A / 2 option B / 3 free text / 4 ask again
  -> decision recorded on Source Issue
  -> resume trigger [develop:resume]
  -> existing worktree resumed [develop:working]
```

If the decision materially changes approved scope, the Product Owner obtains approval for the
revised full Issue Preview before applying `develop:resume`.

## UC-03: Reviewer-required code change

```text
PR [review:ready]
  -> Reviewer claim [review:working]
  -> REQUIRED code fix [develop:resume, review:round-1]
  -> Developer claim [develop:working, review:round-1]
  -> same branch push [review:ready, review:round-1]
  -> Reviewer claim [review:working, review:round-1]
  -> approved [merge:ready]
```

For another required code change, replace `review:round-1` with `review:round-2` and repeat.

## UC-04: Reviewer clarification without a code change

```text
PR [review:working]
  -> ambiguous approved scope [review:clarify]
  -> Slack question and Source Issue decision
  -> same-commit re-review trigger [review:resume]
  -> Reviewer claim [review:working]
  -> approved [merge:ready]
```

The runtime deduplicates this re-review by the recorded decision ID as well as the PR commit SHA.

## UC-05: Reviewer clarification requiring a code change

```text
PR [review:working]
  -> ambiguous approved scope [review:clarify]
  -> Slack question and Source Issue update
  -> code change [develop:resume, review:round-1]
  -> same PR branch resumed [develop:working, review:round-1]
  -> push [review:ready, review:round-1]
```

## UC-06: Review repetition limit

```text
PR [review:working, review:round-2]
  -> another REQUIRED code fix [develop:resume, review:round-3]
  -> push and re-review [review:working, review:round-3]
  -> another REQUIRED code fix [review:clarify, review:round-3]
  -> Slack asks: 1 one more correction / 2 revise plan / 3 stop / 4 ask again
```

Automatic code-fix looping stops at that final clarification. If the user permits another
correction, the Product Owner records the decision and applies `develop:resume` while preserving
`review:round-3`.

## UC-07: Deployment failure

```text
Merged PR [deploy:working]
  -> deployment failure [deploy:failed]
  -> follow-up Issue [develop:ready, followup:deploy]
  -> new recovery flow begins
```

## UC-08: Post-merge E2E failure

```text
Merged PR [e2e:working]
  -> E2E failure [e2e:failed]
  -> follow-up Issue [develop:ready, followup:e2e]
  -> new recovery flow begins
```

## UC-09: Duplicate and delayed events

| Event | Expected outcome |
|---|---|
| Duplicate Slack request, answer, or GitHub event | One durable action is taken; later duplicates observe the same label and state record. |
| Duplicate worker claim | The lease permits only one active Developer or Reviewer per Source Issue. |
| Slack notification failure after a GitHub mutation | GitHub state remains authoritative; the concise Slack notification retries without repeating the mutation. |
| GitHub API temporary failure | The label does not advance; retry follows the existing state. |
