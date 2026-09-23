## `--bugs` shows nothing, not even "none", although a tracking bug exists

- Command: `fx-tests test browser/components/profiles/tests/unit/test_command_line_handler.js --bugs`
- Expected: a Bugs section listing bug 1963207 ("Intermittent browser/components/profiles/tests/unit/test_command_line_handler.js | single tracking bug", RESOLVED INCOMPLETE), or an explicit "no bug found".
- Got: the same output as without `--bugs`, with no Bugs section at all, so I could not tell "no bug" from "flag ignored".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=test_command_line_handler`.

## `fx-tests task` prints an hg revision that hg.mozilla.org no longer serves

- Command: `fx-tests task CaaOSHCERvG9zIdLQZKfDQ --profiles` printed `mozilla-central 8a5eb3c1adf6`; `curl https://hg.mozilla.org/mozilla-central/raw-file/8a5eb3c1adf6.../<path>` returns HTTP 406.
- Expected: a revision I can read the source at.
- Workaround: `curl https://lando.moz.tools/api/hg2git/firefox/<hg sha>` gives the git sha, then `git show <git sha>:<path>` in the local checkout. Printing the git sha too would save this.
