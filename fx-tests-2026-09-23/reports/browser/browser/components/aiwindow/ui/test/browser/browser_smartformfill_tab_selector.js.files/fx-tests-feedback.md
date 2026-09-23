## `--bugs` is silent when no bug names the test

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartformfill_tab_selector.js --bugs`
- Expected: a line saying no Bugzilla bug names this test (or the list of bugs).
- Got: the same output as without `--bugs`, no bug section at all, so "no bug" looks the same as "flag ignored".
- Workaround: `curl -s "https://bugzilla.mozilla.org/rest/bug?summary=browser_smartformfill_tab_selector"` returned `{"bugs":[]}`.

## Question: "which failure mode did each failing task hit?"

- Command: `fx-tests test <path> --task-ids --limit 0` lists the tasks by date without their Issue number; I had to rerun it once per `--issue <n>` (3 runs) to map tasks to failure modes.
- What would have answered it: the issue number next to each task ID in the default `--task-ids` listing.
