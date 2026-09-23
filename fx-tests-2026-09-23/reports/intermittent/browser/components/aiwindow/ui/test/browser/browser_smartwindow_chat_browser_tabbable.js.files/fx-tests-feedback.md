## Question: a passing run of this test on another config, to compare against the failing one

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_chat_browser_tabbable.js --coverage` shows linux2404-64 debug standalone passing 244/244, but nothing gives a task ID for one of those passing runs. `--task-ids` lists failing tasks only.
- Expected: some way to get one passing task (ideally from the same push as a failing task), e.g. `fx-tests test <path> --task-ids --config linux2404-64/debug-mochitest-browser-chrome-standalone --passing`.
- Got: no option for it.
- Workaround: I pulled the push's jobs from the Treeherder API (`/api/jobs/?push_id=…`), fetched each Linux standalone chunk's Taskcluster task definition, grepped `MOZHARNESS_TEST_PATHS` for the manifest, then ran `fx-tests task` on the match. That took about 54 task-definition fetches and a Python script.
