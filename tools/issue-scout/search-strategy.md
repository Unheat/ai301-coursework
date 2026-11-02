# Search strategy: where your scout looks, and why

## Queries

<!-- Primary: Python projects with 1k+ stars and newcomer labels -->
gh search issues --label "good first issue" "stars:>1000" --language python --state open --limit 100 --json title,repository,labels,assignees,commentsCount,updatedAt,url

<!-- Primary: TypeScript projects with 1k+ stars and newcomer labels -->
gh search issues --label "good first issue" "stars:>1000" --language typescript --state open --limit 100 --json title,repository,labels,assignees,commentsCount,updatedAt,url

<!-- Secondary (lower priority): Rust projects with 1k+ stars and newcomer labels -->
gh search issues --label "good first issue" "stars:>1000" --language rust --state open --limit 50 --json title,repository,labels,assignees,commentsCount,updatedAt,url

<!-- Secondary (lower priority): Go projects with 1k+ stars and newcomer labels -->
gh search issues --label "good first issue" "stars:>1000" --language go --state open --limit 50 --json title,repository,labels,assignees,commentsCount,updatedAt,url

## Ranking filters

- drop any result where assignees is not empty (already claimed or in progress)
- drop any result not updated in the last 60 days (active repositories only)
- drop any result with "bounty" in title or labels (crypto/paid spam tasks)
- prefer Python and TypeScript issues over Rust and Go (primary language priority)
- prefer repositories or issues with AI, LLM, or agent keywords in title, labels, or repository name
- prefer fewer comments over more (uncontested newcomer issues)
- tie-break: prefer most recently updated (active maintainers responsive to PRs)

## How many to grade

Grade the top **5** ranked results per scan.