# Evidence guide: where proof lives in a reproduction package

## Environment

### Where it lives
- In an eval bundle: in the `## Candidate repro report` section, typically on an `Environment:` line or table at the top, read against `## Issue` and `## Repo facts`.
- In live mode: at the top of the candidate repro comment, read against the issue's environment fields and the target repository's latest release or branch.

### What good looks like
- The record explicitly lists the tool version, operating system/architecture (e.g. macOS arm64, Ubuntu 22.04), and commit hash or toolchain build.
- If the version tested differs from the issue's reported version (e.g. testing against latest release or main), the delta is explicitly stated. An omitted environment record or vague phrases like "local machine" fail.

## Steps

### Where it lives
- In an eval bundle: the `Steps:` subsection of `## Candidate repro report`.
- In live mode: the reproduction steps block in the student's draft repro comment.

### What good looks like
- Steps provide a complete, self-contained sequence of runnable commands starting from an explicit initial state (such as a fresh clone, clean temporary directory, or minimal configuration file).
- A stranger can re-run them verbatim without guessing unstated parameters, missing private repositories, missing dependencies, or unshared local machine configurations.

## Behavior shown

### Where it lives
- In an eval bundle: the terminal code blocks, command outputs, logs, or error messages inside `## Candidate repro report`, read directly against the `Expected result` and `Current result` in `## Issue`.
- In live mode: the pasted shell output or attached logs in the draft repro comment, compared against the issue's description.

### What good looks like
- The artifact shows the exact error message, exit code, panic traceback, or output delta described in the issue.
- It does not substitute an adjacent or graceful input error (e.g. exit 1/2 argument syntax errors) for the reported bug (e.g. exit 101 panic or crash). Asserting reproduction without including the actual output artifact fails.

## Honesty

### Where it lives
- In an eval bundle: the narrative assertions in `## Candidate repro report` and `## Candidate claim comment` compared directly to the attached output artifacts.
- In live mode: the claims of success or reproduction in the draft comments compared against what the test actually printed.

### What good looks like
- The report claims only what the evidence directly substantiates.
- An honest "cannot reproduce" passes when it provides complete steps, shows the actual output proving the bug did not trigger, and documents what differed (e.g. platform, OS, limits) or includes a control run. Claiming full reproduction when the artifact shows a different error or healthy output fails.

## Comms

### Where it lives
- In an eval bundle: `## Candidate claim comment` and `## Candidate repro report`, read against `## Repo facts` (specifically `contribution policy` and AI guidelines) and `## Issue`.
- In live mode: the student's draft comments compared against the target repository's `CONTRIBUTING.md`, `AI_POLICY.md`, and issue thread.

### What good looks like
- The claim comment identifies the specific version and behavior being investigated, promises an artifact (such as an investigation or repro report), and avoids generic "+1 / please assign" boilerplate or promised timelines ("will fix tomorrow").
- If the repository has a stated policy requiring disclosure of AI assistance, the posted comments explicitly include an AI disclosure statement.
