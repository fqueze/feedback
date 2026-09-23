## `fx-tests test <path> --bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/extensions/formautofill/test/unit/test_rust_autofill_address_bridged_engine.js --bugs`
- Expected: a line such as "Bugs: none name this test" (the `--json` has `annotatedBugs: []`).
- Got: the same output as without `--bugs`; no bug section at all, so "no bug" looks like "flag ignored".
- Workaround: checked `--json` `.annotatedBugs`, and Bugzilla quicksearch.

## Revision printed is hg only, and hg.mozilla.org answers 406 from this machine

- Command: `fx-tests task X0qCmUHPQdKkcPRUbKovsg --profiles` prints `mozilla-central bca8737c308d`; `curl -sL https://hg.mozilla.org/mozilla-central/raw-file/bca8737c308d/<path>` returns HTTP 406 (hg-edge), for any file including tip.
- Expected: a way to read the test at the failing revision.
- Workaround: `curl -sL https://lando.moz.tools/api/hg2git/firefox/<full hg sha>` gives the git sha, then `git show <git sha>:<path>`. Printing the git sha next to the hg one (or using it) would save this step.
