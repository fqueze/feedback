## Question: read the test source at the failing revision

- Command: `fx-tests task aPu0YQW3RcOAbXe-d5NLfw --profiles` prints `autoland 4bb778f39abe`; the brief's `curl -sL https://hg.mozilla.org/integration/autoland/raw-file/4bb778f39abe/<path>` then returns an empty body (hg-edge answers HTTP 406), and the GitHub raw URL with the hg hash is a 404.
- Expected: a revision usable to read the source.
- Got: only the hg hash.
- Workaround: `curl https://lando.moz.tools/api/hg2git/firefox/<hg hash>` gives the git hash, then `git show <git hash>:<path>` in the local checkout. Printing the git hash next to the hg one (it is in the Treeherder push data) would skip this.

## Question: read the source at the failing revision (review)

- Command: `fx-tests task aPu0YQW3RcOAbXe-d5NLfw --profiles` prints `autoland 4bb778f39abe`; `curl https://lando.moz.tools/api/hg2git/firefox/4bb778f39abe` (the short hash printed) returns a 404 HTML page.
- Expected: a hash that the hg2git workaround above accepts.
- Got: only the full 40-character hg hash works, and it only appears inside the Treeherder URL (`revision=4bb778f39abec15122483af81653251181b3b4b3`).
- Workaround: copy the full hash from the Treeherder URL. Printing the full hg hash, or the git hash, on the revision line would avoid this.
