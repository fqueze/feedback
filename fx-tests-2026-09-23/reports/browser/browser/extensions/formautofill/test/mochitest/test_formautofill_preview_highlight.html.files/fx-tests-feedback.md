## `test --bugs` prints nothing at all when it finds no bug

- Command: `fx-tests test browser/extensions/formautofill/test/mochitest/test_formautofill_preview_highlight.html --bugs`
- Expected: a "Bugs" section, or an explicit "no sheriff-annotated bug names this test" line.
- Got: the same output as without `--bugs`, exit 0 — no section, no line on stderr. Cannot tell "none" from "the live query silently failed". A Bugzilla summary search finds 15 bugs naming the test, including its tracking bug 1777089 (closed INCOMPLETE by BugBot).
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=<test file name>'`.

## `task --messages` shows the TEST-KNOWN-FAIL flaky-timeout notices as the failure, not the timeout

- Command: `COLUMNS=400 fx-tests task Sqwzc2-jSkKv9BtTq3pwsg.1 --messages --full-messages`
- Expected: `TIMEOUT Test exceeded time limit` (what `fx-tests test` reports as the issue for this test).
- Got: `1x The author of the test has indicated that flaky timeouts are expected. Reason: Guarantee asynchronous identifyAutofillFields is invoked` and the same with `Intentionally wait for UI ready` — `requestFlakyTimeout` notices logged as TEST-KNOWN-FAIL on every run, pass or fail. The default view also shows the first of them as the failure line.
- Workaround: read the TEST-UNEXPECTED-FAIL marker in the profile.

## The question "the source at this job's revision" has no answer from the revision printed

- Command: `fx-tests task Sqwzc2-jSkKv9BtTq3pwsg.1 --profiles` prints `autoland 4fa3c90aab82`.
- Expected: something fetchable. The brief's `curl -sL https://hg.mozilla.org/integration/autoland/raw-file/<rev>/<path>` returns HTTP 406 with an empty body from this machine (every hg.mozilla.org raw-file / json-rev URL, any User-Agent).
- Got: only the hg hash; GitHub raw needs the git hash.
- Workaround: `curl https://lando.moz.tools/api/hg2git/firefox/<hg rev>` gives `git_hash`, then `https://raw.githubusercontent.com/mozilla-firefox/firefox/<git hash>/<path>` (or `git show <git hash>:<path>` in a local clone). Printing the git hash next to the hg one would save this.

