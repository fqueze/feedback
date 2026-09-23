## Reading the test at the failing revision
- Question: "what did the test look like at this job's revision?"
- Command: `fx-tests task QPUPxGO4Qk6w0P8jbSTjfA.0` prints `mozilla-central 059466678145` (an hg revision).
- `curl -sL https://hg.mozilla.org/mozilla-central/raw-file/<rev>/<path>` redirects to hg-edge.mozilla.org, which answers HTTP 406. The short hash also returns nothing.
- Workaround: `curl https://lando.moz.tools/api/hg2git/firefox/<full hg rev>` gives the git hash, then `git show <githash>:<path>` in the local checkout.
- Could show: the git commit hash next to the hg one, since git is what a local checkout can read.
