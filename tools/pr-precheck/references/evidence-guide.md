# Evidence guide: where evidence lives in a PR package

This guide maps each evidence family to its location in a PR package (in both eval and live modes) and specifies what verifiable, passing evidence looks like.

## Plan fidelity (harness category: silent-drift)

### Where it lives
- **Eval mode**: In the `plan-context` block (`Scope`, `Files to touch`, `Approach`, `Deviations`), read against the candidate PR's `diff`, `commits`, and `description`.
- **Live mode**: In `plan.md` (`## Scope`, `## Files to touch`, `## Approach`, `## Deviations`), read against the output of `git diff main...HEAD`, `git log main..HEAD --oneline`, and `pr_draft.md`.

### What good looks like
- **Footprint match**: Every file touched in the diff is explicitly listed in the plan's files to touch or justified by an explicit deviation note.
- **Scope boundary honored**: The diff does not add unrequested features, new configuration flags, speculative helpers, or unsolicited refactorings that the plan did not scope in.
- **Honest shortfall is acceptable**: If the PR defers a portion of the plan, it passes provided the deferral is explicitly stated in the plan's deviation notes AND honestly disclosed in the PR description (as seen in neovim and minikube).
- **Description fidelity**: The PR description accurately describes what the diff does. It never claims "implements the plan exactly" or "no changes outside X" if extra hunks exist, and never claims documentation or tests were updated if the diff omits them.

## Test evidence (harness category: not-tested)

### Where it lives
- **Eval mode**: In the candidate PR's `test-evidence` section and `description` (under `Testing`), read against the `plan-context` test plan and the issue's reproduction steps.
- **Live mode**: In `test_evidence.md` and `pr_draft.md` (`Testing` section), read against `plan.md` (`## Test plan`).

### What good looks like
- **Decisive observable evidence**: Shows concrete before-and-after command runs, terminal transcripts, row counts (e.g., 41 to 42), status codes, or error messages turning into clean output on the exact bug scenario.
- **Exercises the fixed path**: Evidence must exercise the broken reproduction path, not merely an unaffected control path (e.g., running only a single-file test when the bug requires two files, or testing GET casing when the issue is POST Content-Type).
- **No empty assertions**: Rejects vague assertions like "tested locally", "verified working on my machine", or "tests pass" without supporting transcripts or observable outputs.
- **Repository checks reported**: The repository's standard check suite (unit tests, linter, typecheck) has visible outcomes or honest failure explanations recorded.

## Diff quality (harness category: unreviewable)

### Where it lives
- **Eval mode**: In the candidate PR's unified `diff` and commit list.
- **Live mode**: In `git diff main...HEAD` and `git log main..HEAD --oneline`.

### What good looks like
- **Clean and focused**: The diff contains only the changes necessary to fix the issue and passes review cold in 30 seconds.
- **No debris**: No debugging leftovers (e.g. `console.log`, `print`, `eprintln`), no dead code or unused dummy functions (`_unused_...`, `allow(dead_code)`), and no commented-out trial implementations or leftover TODOs.
- **No formatting churn**: No bulk re-indentation, whitespace changes, or import re-orderings on lines outside the immediate functional fix.
- **Coherent commits**: Commits tell a coherent story rather than exposing raw debugging churn ("wip", "debug", "fmt + cleanup").

## Standards and comms (harness category: standards-wall)

### Where it lives
- **Eval mode**: In the `repo-facts` block (PR template, contributing guidelines, AI policy) read against the candidate PR's `title`, `description`, and `checklist`.
- **Live mode**: In the target repository's `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md` read against `pr_draft.md`.

### What good looks like
- **Template completeness**: Every section required by the repository's PR template (e.g., Summary, Issue/Closes #, Changes, Testing, Quality Checklist) is present and filled with substantive, accurate content rather than being skipped, left blank, or left as placeholder text.
- **Issue linkage**: The issue being resolved is explicitly referenced using the repository's required syntax (e.g., `Closes #<id>` or `Fixes #<id>`).
- **AI-use disclosure**: Where the repository has a stated policy requiring disclosure of AI tools (and in all course coursework), the contributor includes an honest disclosure in their own words detailing how AI was utilized.
- **Maintainer guidance followed**: Any explicit maintainer direction or constraint recorded in the issue thread is acknowledged and respected.
