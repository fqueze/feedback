## `fx-tests test <path> --bugs` prints nothing about bugs when there are none

- Command: `fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_topPicks.js --bugs`
- Expected: a "Bugs" block, or an explicit "no bug names this test" line.
- Got: the ordinary `fx-tests test` output, identical to running without `--bugs`; no way to tell "no bug" from "flag ignored".
- Workaround: searched Bugzilla REST directly.

## Question: "which process hung, and doing what?" for a "child process hang at shutdown" CRASH

- Command: `fx-tests crash <task> <dump> --all-threads`
- Expected: the process type of the dump (GPU / content / RDD...) in the header.
- Got: no process type; had to infer "GPU process" from `GPUParent::ActorDestroy` on the main thread and a thread named `Renderer`, and in one dump from the `Firefox Nightly GPU Helper` module name at the stack bottom.
- Workaround: `--thread 0 --frames 0` and `--thread 9 --frames 0`, grepping each dump.

## `fx-tests crash --thread` takes only an index (review)

- Command: `fx-tests crash XxpEEKc3Rd68tEAKdmJnDw.0 0E3F9799-… --thread 9 --frames 0`
- Expected: `--thread Renderer` (by name) to work, since the question is "what was the Renderer doing" in each of several dumps.
- Got: an index is required, and it differs between dumps, so each needed an `--all-threads` run and a grep first to find it.
- Workaround: `grep -E "^ #[0-9]+ +Renderer"` on the `--all-threads` output.
