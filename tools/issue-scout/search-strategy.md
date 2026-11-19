# Search strategy: where your scout looks, and why

## What changed, and why (2026-11-01, revised 2026-11-19)

My first four queries used `"stars:>1000"`. `gh search issues` has **no** stars qualifier, so GitHub treated `1000` as a literal keyword: four of the top five results were literally issue `#1000` in repos with 1–80 stars (`chaoss/8Knot`, `pyqtgraph`, `INGInious`). **Fix:** removed every star term from the queries and replaced them with `--owner` scoping against the organizations I actually want to work in, plus one keyword fallback for repos outside them. Star/community size is now judged in the repo view during grading, not in search, because search cannot see it.

## Queries

<!-- Traces to: "My primary languages are Python and TypeScript... I want self-contained bug fixes, tool integrations, and testable improvements" -->

Python, applied AI / LLM tooling:

```
gh search issues --label "good first issue" --owner vllm-project --language python --state open --limit 100 --json title,repository,labels,assignees,commentsCount,updatedAt,url
```

<!-- Traces to: "I am interested in applied AI, LLM tooling, agent workflows, and backend/web systems" -->

Python, ML frameworks (PyTorch family, including ExecuTorch):

```
gh search issues --label "good first issue" --owner pytorch --language python --state open --limit 100 --json title,repository,labels,assignees,commentsCount,updatedAt,url
```

<!-- Traces to: "backend/web systems... Python and TypeScript"; established projects I can name -->

Python, backend and build systems:

```
gh search issues --label "good first issue" --owner opensearch-project --language python --state open --limit 100 --json title,repository,labels,assignees,commentsCount,updatedAt,url
```

<!-- Traces to: "TypeScript... established repositories with an active community" -->

TypeScript, full-stack plugin/UI work:

```
gh search issues --label "good first issue" --owner backstage --language typescript --state open --limit 100 --json title,repository,labels,assignees,commentsCount,updatedAt,url
```

<!-- Traces to: "I am also open to Rust and Go for well-scoped systems or CLI tasks, with lower priority" -->
<!-- Fallback for organizations I did not name: keyword-driven, still no star term -->

Rust / Go, systems and tooling:

```
gh search issues --label "good first issue" "system OR CLI OR parser" --language go --state open --limit 50 --json title,repository,labels,assignees,commentsCount,updatedAt,url
gh search issues --label "good first issue" "crate OR parser" --language rust --state open --limit 50 --json title,repository,labels,assignees,commentsCount,updatedAt,url
```

## Ranking filters

Every filter uses only fields `gh search issues --json` already returns:

- drop any result where `assignees` is not empty (already claimed or in progress)
- drop any result whose `updatedAt` is older than 60 days (dead threads, not just dead repos)
- drop any result with "bounty" in the title or labels (paid/crypto spam tasks)
- prefer `python` and `typescript` results over `go` and `rust` (primary language priority)
- prefer results whose title, labels, or repository name mention AI, LLM, agent, or model (applied-AI fit)
- prefer fewer comments over more (fewest comments first — uncontested issues)
- tie-break: prefer the most recently updated (maintainers actually reading the queue)

Star count is deliberately **not** a ranking filter: the search result object has no star field, and using it would require an extra API call per result, which the funnel's cheap-ranking stage forbids. It stays a grading-time signal.

## How many to grade

Grade the top **5** ranked results per scan.
