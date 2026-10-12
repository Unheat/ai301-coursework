# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

**Where it lives.** Eval bundle: the plan's opening cause statement
(usually the first sentence under the candidate plan), read against
the whole repro-evidence block — every step's observation, the
expected-vs-actual pair, and above all any **control** step the block
contains (a run where the blamed component is absent, bypassed, or
unchanged and the behavior differs). Live: the "Diagnosis" section of
`plan.md`, read against the student's posted repro comment on the
issue.

**What good looks like.** The stated cause, taken at face value, would
produce every observation the repro block records, including the
controls; and no control rules it out. Bad: the plan blames component
X while a repro step shows the same success or failure with X out of
the loop (the classic wrong-cause trap — the package's own control
rules the diagnosis out); or the plan restates the symptom as the
cause ("it fails because it errors"); or the plan states no cause and
jumps straight to a change. The cause must also explain the *actual*
behavior, not just the expected one.

## Scope

**Where it lives.** Eval bundle: the plan's change description and any
explicit in/out lines (phrases like "In: … Out: …", "This will not
touch…", "deferred because…"). Live: the "Scope" / "Scope and
non-goals" section of `plan.md`.

**What good looks like.** One can draw a line around the change: every
work item in the plan serves the issue's reported problem, and work
beyond it is either explicitly deferred with a reason or ruled out by
an out-of-scope line. Bad: a one-symptom fix wrapped in a redesign —
bundled refactors, dependency migrations, config systems, UI reworks,
new options, cross-platform abstraction layers, CI changes — the
signature is several separately-reviewable changes where the issue
asked for one. Deferring related-but-untestable or
maintainer-unrequested work *with a reason* is good scoping, not a
miss. Judging "serves the issue" is about the work items themselves,
never about how many paragraphs the plan uses to describe them.

## Executability

**Where it lives.** Eval bundle: the plan's change description — named
files, functions, or areas, and the chosen approach. Live: the "Files
to touch" and "Approach" sections of `plan.md`.

**What good looks like.** A stranger could open the repo and start:
the plan commits to specific files or areas (or, where the artifact
genuinely cannot name files, to one chosen approach) for everything it
changes. Bad: real build decisions left open at build time — "somewhere
in the input stack", "gocui? tcell? not sure", "upstream or vendored,
whichever is easier", "profile first, then decide", "maybe also check
other linters". "Investigate the area" is not an approach; an approach
is a committed answer with room for the investigation to correct it.

## Test plan

**Where it lives.** Eval bundle: the plan's test section, read against
the repro block's steps and expected-vs-actual. Live: the "Test plan"
section of `plan.md`.

**What good looks like.** A named, observable result that differs
between broken and fixed — typically the repro re-run with its
expected outcome now holding (a color flips, an exit code changes, an
error line becomes a success line), or a fixture/case with a specific
asserted output. The strongest plans map their test onto the repro
steps explicitly and add adjacent cases sharing the code path. Bad:
"run the full test suite", "nothing else should feel broken", "should
feel fast" — none of these would notice if the fix did nothing. A test
plan that covers only part of what's in scope is fine if the plan
explicitly scoped the rest out with a reason; it is not fine if the
plan claims the work and the test never observes it.

## Honesty

**Where it lives.** Eval bundle: the plan's deferral and unknown
language ("deferred because…", "unknown: …", "arguable") and the match
between what the comment claims and what the plan and repro evidence
support. Live: the "Risks and unknowns" and "## Deviations" sections
of `plan.md`.

**What good looks like.** Uncertainty is labeled as uncertainty:
deferrals carry reasons, unknowns are named as unknowns with a check
that will resolve them, and the comment claims only what the repro
evidence shows (a comment that says "reproduced" must be backed by a
repro block that shows it). Bad: confident language ("clearly",
"definitely") over an unproven mechanism, or a build deviation that
exists only in the diff. In this rubric honesty surfaces inside the
other checks — an overclaimed diagnosis fails grounded-cause, an
untested promise fails decisive-test — rather than as a standalone
check.

## Comms

**Where it lives.** Eval bundle: the candidate plan comment, read
against (a) the thread highlights — every maintainer or issue-author
comment, especially any naming a culprit, requesting a check or
workaround, requesting testing, or pointing at prior art (open PRs) —
and (b) the repo-facts block's contribution and AI-use policies.
Live: the draft `comment.md`, read against the live issue thread and
the repo's CONTRIBUTING/template statements.

**What good looks like.** The comment shows the author read the room:
it acknowledges and follows (or explains a respectful departure from)
explicit maintainer direction; it engages named prior art instead of
racing it; it meets every stated policy requirement — most
consequentially, disclosing AI use where the repo's policy requires
disclosure, which some repos require of every contribution. Bad: a
comment that proposes work contradicting the maintainer's named
culprit or requested next step without acknowledging it; boilerplate
that would read identically on any issue; or a missing disclosure the
repo's stated AI policy requires. Thread awareness is judged on the
comment's content against the specific signals present, never on
tone or length.
