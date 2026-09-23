## Question: the test's source at the failing revision

- Command: `fx-tests task fPhvUgCdSaqkS4mDjLtOOQ --profiles` prints `autoland f56e7e58b60c` (an hg revision).
- Expected: to fetch the test at that revision (the brief says `curl -sL https://hg.mozilla.org/integration/autoland/raw-file/<rev>/<path>`).
- Got: hg.mozilla.org redirects to hg-edge.mozilla.org, which answers HTTP 406 with an empty body (also with a full hash
  and a browser User-Agent). The local checkout is git and does not know the hg hash.
- Workaround: `curl -s https://lando.moz.tools/api/hg2git/firefox/<hg hash>` gives the git hash, then
  `https://raw.githubusercontent.com/mozilla-firefox/firefox/<git hash>/<path>` (or `git show <git hash>:<path>` locally).
- What would have answered it: `fx-tests task` printing the git hash next to the hg one (or a raw-file URL that works).
