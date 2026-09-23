## `--bugs` lists no bug although one names the test

- Command: `fx-tests test widget/tests/test_bug596600.xhtml --bugs`
- Expected: bug 1794257 ("Intermittent widget/tests/test_bug596600.xhtml | single tracking bug") listed.
- Got: the normal `test` output with no bugs section at all, not even "none found".
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=test_bug596600'`.

## Question: which push was the last to fail, and which push stopped it?

- For a test that stopped failing, the brief asks for the landing that stopped it, which needs the revision and push time of the last failing run.
- Command: `fx-tests test <path> --task-ids --limit 0` gives task IDs grouped by date only.
- Workaround: one `curl` per task to the Taskcluster queue for `GECKO_HEAD_REV` and `created` (16 calls), then `json-pushes` on hg.
- What could have shown it: the revision (and repo) next to each task ID, or a "last failure: <repo> <rev> <push time>" line under `--history`.
