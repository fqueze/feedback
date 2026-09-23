## `fx-tests test <path> --bugs` prints no bugs block when none is found

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_aiwindow_stop_generation.js --bugs`
- Question: which sheriff-annotated bugs name this test?
- Expected: a `Bugs` block, saying "none" when there are none.
- Got: the same output as without `--bugs` (verdict, configs, issues), nothing else. Indistinguishable from the flag being ignored or the lookup failing silently.
- Workaround: Bugzilla REST quicksearch by hand.

## No way to get a passing job's task ID for a test on a config

- Question: on the config where the test fails (linux2404-64-shippable swr), how long does the gap between two tests in the same browser usually take in passing runs? That is the timing the failure depends on (an engine idle timeout of 120 s created by an earlier test in the chunk).
- Tried: `fx-tests test <path> --task-ids`, `--profiles`, `--durations`: all only list failing tasks, or give per-test durations, not jobs.
- Could have shown: a few passing task IDs per config (or `--task-ids --passing`), so their resource-usage profiles can be compared with the failing ones.

