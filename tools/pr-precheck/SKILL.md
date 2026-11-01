---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

This tool answers exactly one question about a candidate pull request: **is this ready to submit?**

A PR package consists of the candidate pull request's title, description, commit history, unified diff, and test evidence, evaluated against the posted plan it claims to implement, the issue that motivated the work, and the repository's contributing standards. The tool evaluates the submission objectively using the rubric and procedure defined in this directory.

## Inputs and modes

The tool operates in two distinct modes:

- **Live mode**: Used to pre-check your own pull request before opening it. The inputs are:
  - Your posted plan with any recorded deviation notes (`plan.md`, or the house plan for house-chain students).
  - The branch's committed diff relative to the default branch, produced by `git diff main...HEAD`.
  - The draft pull request title and description (`pr_draft.md`).
  - Captured test evidence and command outputs (`test_evidence.md`).
  - The issue thread, reproduction pack, repository PR template, and contributing policies from the live repository.
- **Eval mode**: Used by the evaluation harness to grade frozen PR package bundles (`pkg-XX` or `calib-XX`). In eval mode, the package bundle text is the entire world. Do not fetch or read external files, repositories, or network resources. Execute every check and apply the complete verdict rule to the bundle text.

## The scope seam (live mode only)

In live mode, read `scope.md` before processing any other files:
- Verify that the target repository matches the `Repo:` declaration in `scope.md`.
- If the `Repo:` line in `scope.md` contains an unfilled placeholder (such as `<ORG>/<PATH-REVIEW-REPO>`), stop immediately without grading and instruct the user to set their section's Path Review repo in `scope.md`.
- Refuse to grade pull requests targeting repositories outside the declared scope.
- In eval mode, ignore `scope.md` completely.

## The voice seam (live mode only)

In live mode, read `voice-guide.md`:
- Review the candidate PR title and description against the rules in `voice-guide.md`.
- Report any violated voice rules or anti-slop guidelines in the readable summary.
- The voice guide provides advisory feedback and does not alter the binary verdict unless a specific rubric check evaluates it.
- In eval mode, ignore `voice-guide.md` completely.

## Component reads

The tool executes through four coordinated components:
- `rubric.md` defines the checks table, pass conditions, weights, and the verdict rule.
- `references/evidence-guide.md` maps where each evidence family lives in the package and what passing evidence looks like.
- `procedure.md` defines the sequential read order, evidence gathering pairings, check execution, and verdict assembly.
- Refusal rule: If `rubric.md` or `procedure.md` has no substantive content, refuse to grade and report that the tool is missing required rubric or procedure instructions.
- If `procedure.md` is silent on a necessary step, report the procedural gap rather than improvising.

## Verdict and output

The verdict space is binary: `accept` (ready to submit) or `reject` (hold).

The tool must conclude its reply with a fenced JSON block adhering strictly to the schema below, with no text or formatting following it:

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

## Grading discipline

Every evaluation must adhere to the following contract guarantees:
- **Evidence first**: Never assign a grade without quoting or citing the concrete fact that decided it.
- **Grade the thing, not the polish**: Evaluate code changes, plan fidelity, and observable evidence directly; do not judge based on superficial formatting or prose tone.
- **The rubric decides**: Follow the rubric's pass conditions and verdict rule strictly; personal impressions do not override rubric conditions.
- **The procedure decides how**: Follow `procedure.md` step by step.
- **Unclear defaults to fail**: Any unverifiable claim, missing evidence, or ambiguous test output must receive `unclear`, which counts as `fail` under the verdict rule.
