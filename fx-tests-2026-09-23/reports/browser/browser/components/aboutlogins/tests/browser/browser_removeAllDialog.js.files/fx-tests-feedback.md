## `fx-tests test <path> --bugs` prints no bug section

- Command: `COLUMNS=250 fx-tests test browser/components/aboutlogins/tests/browser/browser_removeAllDialog.js --bugs 2>&1`
- Expected: a list of bugs naming the test (Bugzilla has 7, e.g. 1677894 whose summary is exactly the top failure message, and 1775757 with a patch landed 2026-09-01), or a line saying none were found.
- Got: the same output as without `--bugs` (verdict, per-config table, Issues); no bug section, no "no bugs" line, exit 0.
- Workaround: `curl -sL "https://bugzilla.mozilla.org/rest/bug?summary=browser_removeAllDialog.js&include_fields=id,summary,status,resolution"`.

## Question (review): "the git commit of this job's revision, to read the code at it"

- Command: `fx-tests task e_AF-xCZQj-OCPHBNTUR7g --profiles`
- Expected: the revision in a form I can read the code at.
- Got: only the hg revision (`autoland 53d576ce0c25`). `curl https://hg.mozilla.org/integration/autoland/raw-file/<rev>/<path>` (and mozilla-central) returned HTTP 406 here, and the local checkout is git.
- Workaround: `curl https://lando.moz.tools/api/hg2git/firefox/<full hg hash>`, then `git show <git hash>:<path>`.
- What would have answered it: the git hash printed next to the hg one.
