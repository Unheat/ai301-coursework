# Procedure: how this tool grades a PR package

This procedure defines the exact execution stages to evaluate a candidate pull request against its plan, issue, and repository standards.

## Read order

Execute the read in four strictly sequential steps before grading any checks:

1. **Read the plan context first**:
   - In eval mode, read the `plan-context` block. In live mode, read `plan.md`.
   - Note the issue reference, the stated Scope (In and Out), the expected Files to touch, the planned Approach, the Test plan, and any recorded Deviations.
2. **Read the repository standards**:
   - In eval mode, read the `repo-facts` block. In live mode, read `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`.
   - Note the required PR template sections, issue reference format (e.g. Closes #), and whether the repository has a stated AI-disclosure policy.
3. **Read the diff and commit history**:
   - In eval mode, read the candidate PR's unified diff and commit list. In live mode, read `git diff main...HEAD` and `git log main..HEAD --oneline`.
   - Note every file touched, the number of changed lines, and the substance of each hunk (functional fix, new flags, helper routines, comments, debug prints, or formatting).
4. **Read the PR title, description, and test evidence**:
   - In eval mode, read the candidate PR's title, description, and test evidence. In live mode, read `pr_draft.md` and `test_evidence.md`.
   - Note the claimed changes, before/after transcripts, repo check outputs, and Notes for Reviewers (including AI disclosure).

## Evidence gathering

Perform six concrete side-by-side evidence pairings per `references/evidence-guide.md`:

1. **Diff footprint against plan scope (Plan fidelity)**:
   - Match each file in the diff against the plan's `Files to touch`.
   - Check if the diff adds unrequested flags, features, or renames.
   - If a discrepancy exists (extra file, unrequested flag, or omitted promised task), check whether an explicit note in the plan's `Deviations` accounts for it.
2. **Description claims against diff contents (Description fidelity)**:
   - Check whether the description claims more than the diff delivers (e.g., claiming documentation or tests that are not present in the diff).
   - Check whether the description falsely claims "exact plan fidelity" when unannounced extra hunks or flags are present.
3. **Test evidence against plan and issue repro (Decisive testing)**:
   - Check whether the test evidence shows observable before-and-after execution output on the specific bug scenario identified in the issue and plan.
   - Verify that the evidence exercises the broken path rather than an unaffected control path.
   - Flag any vague claims ("tests pass", "verified locally") that lack terminal transcripts.
4. **Repo verification checks (Repo checks)**:
   - Check whether repository standard checks (unit test suite, linter, typecheck) have visible command outputs or honest failure explanations.
5. **Diff hygiene scan (Diff quality)**:
   - Inspect all diff hunks for debugging statements (`console.log`, `eprintln`, `print`), dead or unused code (`allow(dead_code)`, `_unused_...`), commented-out trial code, or unrelated formatting/import churn.
6. **Template and policy compliance (Standards & comms)**:
   - Verify that all required template sections and checklists are filled with substantive content.
   - Check if an AI-disclosure statement is present in the contributor's own words whenever required by repo policy or course guidelines.

## Check execution

Grade the rubric checks in exact rubric order:
1. `plan-fidelity`: Grade using pairing 1. Deliberately deferred work recorded in plan deviations passes; silent scope creep fails.
2. `description-fidelity`: Grade using pairing 2. Description must truthfully represent the diff.
3. `decisive-testing`: Grade using pairing 3. Must show observable before/after on the reproduction case.
4. `repo-checks`: Grade using pairing 4. Repository test/lint checks must be visibly reported.
5. `clean-diff`: Grade using pairing 5. Must be free of debug code, dead code, and churn.
6. `template-compliance`: Grade using pairing 6 (template structure).
7. `ai-disclosure`: Grade using pairing 6 (AI policy requirement).

For each check:
- If evidence satisfies the pass condition, assign `grade: "pass"` and provide a concise evidence sentence quoting the supporting fact.
- If evidence violates the pass condition, assign `grade: "fail"` and provide a concise evidence sentence quoting the offending line, missing element, or unnoted deviation.
- If evidence is missing or cannot be verified, assign `grade: "unclear"` (treated as fail).

## Verdict assembly

1. **Apply verdict rule**:
   - If every required check receives `pass`, the final verdict is `accept`.
   - If one or more required checks receive `fail` or `unclear`, the final verdict is `reject`.
2. **Identify deciding check**:
   - If the verdict is `reject`, identify the first failing required check in rubric order as the deciding check.
3. **Format output**:
   - Emit a readable per-check summary.
   - Conclude with the valid fenced JSON block as specified in CONTRACT.md, with nothing following it:
```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```
