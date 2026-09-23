## Question: what does the code look like at the failing revision?

- Command: `fx-tests task MNzWhEAfRI69CzatU2nBWg --profiles` prints `autoland ad9dc82bc357` (hg hash).
- Expected: a revision I can read the code at.
- Got: `curl -sL https://hg.mozilla.org/integration/autoland/raw-file/ad9dc82bc357/<path>` now returns HTTP 406 (via hg-edge), and the hg hash is not in the local git checkout nor on GitHub.
- Workaround: `curl https://lando.moz.tools/api/hg2git/firefox/<full hg hash>` for the git hash, then `git diff <git hash> HEAD -- <paths>` locally.
- What would have answered it: printing the git hash next to the hg one in `fx-tests task` (and `test --task-ids`).
