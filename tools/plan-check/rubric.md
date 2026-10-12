# Rubric: is this plan ready to post and build from?

<!--
Checks map one-to-one onto the failure families the eval set is built
from: wrong-cause (grounded-cause), scope-creep (bounded-scope),
unbuildable (startable, decisive-test), thread-convention
(thread-direction, repo-policy). Clear accepts are terse but complete,
so no check grades length, headings, or polish.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| grounded-cause | The plan's stated cause read against the repro-evidence block's recorded observations, including any control steps that show the behavior absent when a component is bypassed or removed. | The stated cause is consistent with every observation the repro evidence records and would produce the reported actual behavior; fail when the evidence contains an observation the cause contradicts or cannot account for (for example, a control run that succeeds without the blamed component), or when the plan states no cause at all. | required |
| bounded-scope | The plan's change description and any in-scope/out-of-scope lines, read against the issue's reported problem. | Everything the plan changes serves the reported issue; work beyond it is explicitly deferred with a stated reason or ruled out by an out-of-scope line; fail when a fix for the reported issue is bundled with unrelated rework (refactors, migrations, redesigns, new features, while-in-the-area cleanup) into the same change. | required |
| startable | The plan's approach, named files or areas, and stated order of work. | A stranger could start executing without asking the author anything: the plan commits to specific files, areas, or one chosen approach for everything it changes; fail when a decision needed to build is left open at build time ("somewhere in the input stack", "upstream or vendored, whichever is easier", "investigate first"). | required |
| decisive-test | The plan's test plan read against the repro evidence's steps and its expected-vs-actual. | The test plan names an observable result that differs between broken and fixed behavior for what is in scope (for example, the repro's expected outcome now holding, a specific output, exit code, or rendered state); fail when it names no observable outcome ("run the full test suite", "nothing else should feel broken", "should feel fast") or only restates that the change works. | required |
| thread-direction | The plan comment (and the plan, where the comment defers to it) read against the maintainer and issue-author signals in the thread highlights. | The comment engages with, or at minimum does not contradict or ignore, explicit direction from a maintainer or the issue author in the thread (a named culprit, a requested check, a requested workaround, prior art to build on); fail when such direction exists in the thread and the comment neither acknowledges nor follows it. When the thread carries no explicit direction, this passes. | required |
| repo-policy | The plan comment read against the repo-facts block's stated contribution policy and AI-use policy. | The comment meets every requirement the repo facts state for outside contributions (for example, disclosing AI use where the policy requires disclosure, following a stated comment or PR format); fail only when the repo facts state a requirement and the comment does not meet it. When the repo facts state no applicable requirement, this passes. | required |

## Verdict rule

Accept (ready) if and only if every required check passes. Any single
required check graded `fail` holds the package: verdict `reject`.
`unclear` counts as `fail` — a plan whose readiness cannot be verified
from the package is not ready to build from. There are no `preferred`
checks in this rubric, so no check is ever verdict-neutral.
