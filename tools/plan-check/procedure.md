# Procedure: how this skill grades a plan package

## Read order

1. **Eval mode** (a package bundle): read the bundle top to bottom in
   this exact order: (a) the header lines — source repo, capture date;
   (b) the repo-facts block; (c) the issue; (d) the thread highlights;
   (e) the repro-evidence block; (f) the candidate plan; (g) the
   candidate plan comment.
2. **Live mode**: read `scope.md` first and confirm the issue URL is in
   scope (stop if not). Then read, in order: the issue thread on GitHub
   (top post, then maintainer/author comments), the student's posted
   repro comment on that issue, the student's `plan.md`, the draft plan
   comment. `references/evidence-guide.md` names what to pull from
   each.
3. **While reading the repro evidence (both modes), write down:**
   (i) the environment it pins; (ii) each numbered step and what it
   observed; (iii) the expected-vs-actual pair; (iv) any **control**
   step — one where the blamed component is absent, bypassed, or
   unchanged and the behavior differs accordingly. Controls are the
   strongest cause evidence; list them explicitly.
4. **While reading the thread highlights, write down every explicit
   direction** from a maintainer or the issue author: a named culprit
   location, a requested check or workaround, a request for testing, or
   named prior art (open PRs, linked fixes). If there are no comments,
   record "no thread direction".
5. **While reading the repo facts, write down every stated
   requirement** the repo puts on outside contributions: template asks,
   comment/PR conventions, AI-use disclosure or AI-comment policy.
6. Read the plan and comment **last, and only after steps 3-5 are
   written down.** The reason for this order: the repro evidence and
   thread pin down the facts first, so the plan is graded against
   pinned behavior instead of the plan's own narrative anchoring the
   read.

## Evidence gathering

7. Before grading any check, gather and quote the evidence for all six
   checks. For each check, record one or more direct quotes plus where
   each quote came from:
   - **grounded-cause**: quote the plan's stated-cause sentence; quote
     each repro observation it must be consistent with, and every
     control step.
   - **bounded-scope**: quote the change description; quote any
     in-scope/out-of-scope lines; list every distinct work item the
     plan contains (each separately changeable piece counts).
   - **startable**: list the files, areas, or approach the plan names;
     list any decision the plan leaves to build time.
   - **decisive-test**: quote the test-plan steps and the stated
     expected outcome; note which repro step or expected-vs-actual
     pair each test maps onto.
   - **thread-direction**: quote each explicit thread direction (or
     "no thread direction"); quote what the comment says, if anything,
     in response.
   - **repo-policy**: quote each stated requirement from the repo
     facts (or "no stated requirement"); note whether the comment
     meets it.
8. In live mode, take the reproduction evidence from the student's
   posted repro comment on the issue, exactly as SKILL.md directs; if
   the drafts quote no repro evidence at all, record that absence —
   the checks that need repro evidence grade on it and will fail.
9. Do not fetch anything in eval mode; the bundle is the whole world.
   In live mode, do not read files beyond the drafts, the thread, and
   the posted repro comment: the package is what the drafts contain
   and quote.

## Check execution

10. Grade the checks in table order (grounded-cause, bounded-scope,
    startable, decisive-test, thread-direction, repo-policy), one at a
    time, applying each check's pass condition to the gathered quotes
    only.
11. Each check's grade is `pass`, `fail`, or `unclear`, decided
    strictly by its pass condition — not by overall impression, and
    not by the plan's length, structure, or headings. A terse plan
    that satisfies every pass condition passes every check.
12. Absence rules (so two executors grade the same package the same
    way):
    - Evidence a check's claim **needs** is absent → that check
      fails, not unclear: no stated cause fails grounded-cause; no
      named files, areas, or chosen approach fails startable; no
      observable outcome fails decisive-test; no repro evidence quoted
      in the drafts fails grounded-cause.
    - Things a check only flags **when they exist** are absent → the
      check passes vacuously: no thread direction passes
      thread-direction; no stated repo requirement passes repo-policy;
      no extra work items means bounded-scope is judged on the one
      change alone.
    - `unclear` is only for genuine conflict or ambiguity in gathered
      evidence (for example, two quotes that point opposite ways and
      the package gives nothing to break the tie).
13. A check may be graded from its gathered quotes without re-reading
    the whole package. Re-read the source section only if two quotes
    seem to conflict; if the re-read does not resolve the conflict,
    grade `unclear`.
14. If the draft plan comment breaks a rule in `voice-guide.md` (live
    mode only), note it in the summary with the rule quoted; this
    never changes a check's grade unless a rubric check reads it.

## Verdict assembly

15. Apply the rubric's verdict rule exactly: accept if and only if
    every required check is `pass`.
16. Convert every `unclear` to `fail` before applying the rule, per
    the verdict rule.
17. If the verdict is `reject`, name in the summary every check that
    failed, with its deciding quote.
18. Emit the JSON block per SKILL.md: one entry per check, in table
    order, each with a one-line evidence quote; `verdict` last. The
    JSON block is the last thing in the output.
