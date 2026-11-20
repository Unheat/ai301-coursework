# Scope: where to look, and who is looking

<!--
This is the real-world scope. It replaces the sandbox scope.md in
your installed skill: the field is no longer the Path Review repo,
and the house rules do not travel. Like every scope file, this one is
the skill's field of view: the rubric (rubric.md) decides whether an
issue is GOOD; the scope decides which issues are candidates at all,
and the strategy (search-strategy.md) decides where the scout
actually walks. Live mode only: in eval mode the bundle is the whole
world and this file is ignored.
-->

## Where candidates come from

Any public repository. The sandbox is behind you; the field is now
all of GitHub, and your fit profile below is what keeps that
searchable. Start from repos in languages and tools you named there,
and let the profile steer where you search, not just how results are
ranked.

**Claims count again.** The Path Review house rule does not travel.
Out here a claim comment or an assignee is a stranger who got there
first, and you have no channel to coordinate with them. Read claim
signals exactly as the rubric's checks state them; do not carry the
classroom habit of claiming over someone.

**Claim upstream the moment you commit.** The rule cuts both ways:
the instant you decide to take an issue, say so on the issue itself
(a short comment: what you plan to do and roughly when). You are now
the claim signal someone else's skill will read. Deciding privately
and claiming later is how two strangers do the same work twice.

## Your fit profile

<!-- Paste this forward from your sandbox scope.md, and update it:
it now does more work. Besides ranking the issues your rubric
accepts, it steers WHERE the scout searches: your search-strategy.md
queries should each trace to a sentence here, so name the languages,
tools, and project areas you actually want to work in, and anything
you want to avoid. It still never changes a verdict: fit cannot
rescue an issue your rubric rejects, and cannot sink one it
accepts. -->

I am interested in applied AI, LLM tooling, agent workflows, and backend/web systems. My primary languages are Python and TypeScript; I am also open to Rust and Go for well-scoped systems or CLI tasks, with lower priority than Python and TypeScript. I want self-contained bug fixes, tool integrations, and testable improvements where the expected behavior is clear and verifiable, and I avoid trivial translation tasks, inactive hobby projects, and massive multi-month refactors.

Revised 2026-11-19: I originally wrote "at least 1,000+ stars" here. That preference contradicted the project I actually chose (`opensearch-build` has 212 stars) and it turned out to be the wrong signal anyway — the thing I care about is whether maintainers are alive and the work is real. So the profile now reads: an established, actively maintained repository, ideally organization-backed, with a non-bot commit in the last 90 days and a review queue that moves. Star count is a weak ranking signal, never an exclusion rule: a 212-star repo pushed to daily is a better place to contribute than a 5,000-star repo with no recent commits. The `stars:>1000` search term is gone from `search-strategy.md` for a second, separate reason — `gh search issues` has no star qualifier at all, so it only ever matched issue `#1000`.
