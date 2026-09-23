## Question: read the test and product code at the failing revision

- Command: `fx-tests task UhgvDTthTv2uMQ1Tx7aRAA --profiles` prints `autoland de709eaee734` (a Mercurial hash).
- Expected: a revision that can be read, per the brief with `curl -sL https://hg.mozilla.org/integration/autoland/raw-file/<rev>/<path>`.
- Got: hg.mozilla.org answers HTTP 406 for every raw-file/json-rev URL (also for mozilla-central tip); the hg hash is not in the local git checkout either.
- Workaround: `curl https://lando.moz.tools/api/hg2git/firefox/<hg rev>` gives the git hash (a7f1d35aa6e5), then `git show <git hash>:<path>`.
- What the output could show: the git hash next to the hg one.

## Question: does any bug name this test?

- Command: `fx-tests test <path> --bugs`
- Expected: a line saying no annotated bug was found.
- Got: the same output as without `--bugs`, with no bugs section at all; only `--json` shows `"annotatedBugs": []`.
- Workaround: `--json`.
