## Question: does any bug name this test?

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_jump_to_bottom.js --bugs`
- Expected: a Bugs section, or a line saying no bug names the test.
- Got: the same output as without `--bugs`, and no Bugs section at all, so "none found" cannot be told apart from "the flag did nothing".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?quicksearch=<file>` and `?summary=<name>`, which both came back empty.

## Question: which configs does one failure mode hit, and how often?

- Command: `fx-tests test <path> --task-ids --limit 0 --issue 2`
- Expected: a per-config count for the issue, since the verdict's config table mixes every failure mode together.
- Got: a flat, per-day list of task IDs and job names, with `×2` for reruns.
- Workaround: an awk script over the list, stripping the chunk suffix and summing `×2` as 2.
- What the default output could show: a `config  jobs  failures` summary above the task list when `--issue` is given.
