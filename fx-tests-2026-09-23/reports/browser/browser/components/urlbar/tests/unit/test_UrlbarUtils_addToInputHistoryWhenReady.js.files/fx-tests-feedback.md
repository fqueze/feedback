## `--bugs` prints no bug section at all when none match
- Command: `fx-tests test browser/components/urlbar/tests/unit/test_UrlbarUtils_addToInputHistoryWhenReady.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test".
- Got: output identical to the run without `--bugs`; nothing says whether the search ran and found nothing.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?quicksearch=<test file name>` returned `{"bugs":[]}`.

## Issues list does not flag job-wide breakage
- Question: "is this failure mode about this test, or did the whole job break?"
- Command: `fx-tests test <path>` Issues list, then `fx-tests task <id> --profiles` on each task.
- Got: issue 3 (TIMEOUT, 3x) is three windows11-64 opt-xpcshell-msix jobs where 641-1563 tests timed out; issue 4 (NS_ERROR_FILE_CORRUPTED readUserPrefsFromFile, 2x) is one windows11-32 job with 307 failing tests. They are listed with the same weight as the two test-specific modes; only running `task` on each revealed it.
- Could show: the number of failing tests in the job next to each issue, or a "job-wide" marker when the job had hundreds of failures.

## `task` prints the hg revision; the brief's hg raw-file URL returns 406
- Command: `curl -sL https://hg.mozilla.org/integration/autoland/raw-file/3f9ceff4a46d.../<path>` -> HTTP 406 from hg-edge (with and without full hash, UA or Accept headers).
- Workaround: `curl https://lando.moz.tools/api/hg2git/firefox/<hg sha>` gave the git sha, then `git show <git sha>:<path>` locally.
- Could show: the git sha next to the hg revision in `fx-tests task` output.
