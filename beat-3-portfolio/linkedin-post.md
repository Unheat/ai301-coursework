This month I opened my first pull request in OpenSearch, an open source search project, to fix part of a bug in its build tooling that has been open since 2021. The tools were switching the folder the program was working in while it ran, and every other task in the same process could see that change.

I found the issue with a small tool I built that searches open source projects for beginner-friendly bugs, posted a reproduction showing the folder change leaking to another part of the program running at the same time, and wrote a test that fails on the old code and passes on my fix. The change hands the folder to each command directly instead of switching it globally, and it covers three call sites; the two harder ones are deliberately left for a follow-up.

A quality gate I built for my own submissions caught that my pull request description claimed five tests proved something only two of them did. I added the missing assertions to the tests rather than soften the sentence, and the gate passed on its next run.

The pull request is open and awaiting review. Twice this term, tools I built caught my own writing drifting from what the work was, and both times the fix was to change the work, not the words.

https://github.com/opensearch-project/opensearch-build/pull/6573
#OpenSource #Python #Testing
