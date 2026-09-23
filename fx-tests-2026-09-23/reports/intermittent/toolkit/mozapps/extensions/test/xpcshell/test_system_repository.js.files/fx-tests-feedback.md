## Question: at which git commit do I read the code of this failing task? (review-test_system_repository.js)

- Command: `fx-tests task Z3PNcOADQrWbSgfX4kZNdA --profiles`.
- Expected: a revision I can read files at.
- Got: `autoland ad9dc82bc357` (short hg hash). `https://hg.mozilla.org/integration/autoland/raw-file/<rev>/<path>` returned HTTP 406 with an empty body. lando's `api/hg2git/firefox/<hash>` needs the full 40-char hash, which is only in the Treeherder URL line.
- Workaround: take the full hash from the Treeherder URL, map it with `curl https://lando.moz.tools/api/hg2git/firefox/<full hash>`, then `git show <git hash>:<path>`.
- What would have answered it: `fx-tests task` printing the full hg hash and the git commit.
