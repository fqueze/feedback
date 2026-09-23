# profiler-cli feedback (test_bookmark_abort_merging.js)

## Default session directory not writable in the agent sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=test_bookmark_abort_merging.js profiler-cli load <taskcluster url> --session test_bookmark_abort_merging.js-1`
- Expected: the profile loads.
- Got: `Error: Cannot create the profiler-cli session directory /Users/florian/.profiler-cli. Permission denied.` The brief does not mention `PROFILER_CLI_SESSION_DIR`, and `profile-link.py` reads the same variable.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command (the directory other agents on this machine already use).

## Question: "in what exact order, to the microsecond, did these markers happen?"

- Command: `profiler-cli thread markers --list` / `profiler-cli marker info m-3725 m-3726`
- The failure hinges on a 1.3ms gap between two instant markers and on 23-50μs gaps in the passing merges. The text output prints `t=1.029s` / `t=1.030s` (rounded to the ms once past 1s), so the order and gap could not be read from it.
- Workaround: `marker info <m> --json` per marker, and read `start`.
- What the output could show: sub-ms precision on `t=` (e.g. `t=1028.671ms`) at least when neighbouring rows share the same rounded value, or a `--precise` flag.

## `marker info --json`: `start` and `context.currentViewRange.start` use different origins

- Command: `profiler-cli marker info m-230 --json`
- Got: `"start": 1028.18` (profile-relative, matches `t=1.028s`) next to `"currentViewRange": {"start": 1039.36, ...}` and `"rootRange": {"start": 46.36, ...}` (absolute). Comparing them directly places the marker outside the view.
- Workaround: subtract `rootRange.start` from the range values.

## Question: "which task on this thread was the dispatch of that runnable?"

- Command: `profiler-cli thread markers --list` with `TaskController::AddTask (flow=...)` and `Runnable ... task: ...`
- To tie a `Runnable` to the `TaskController::AddTask` that queued it I grepped the dumped list for the flow id. Flow ids are pointers and get reused many times per ms, so the matching (the latest AddTask with that id before the Runnable) had to be done by hand.
- What the output could show: `marker info` on a `Runnable` naming the `AddTask`/`PutEvent` marker with the same flow that most recently preceded it on the thread, or a `--flow <id>` filter.

## Question: "what was the machine's CPU use while this test ran?" (review-test_bookmark_abort_merging.js)

- Command: `profiler-cli thread markers --search "CPU Use" --list --limit 0` on a `profile_resource-usage.json`, zoomed to the test's range.
- Got: one row per 100ms marker with name, start and duration only; `cpuPercent` is not in the row. I had to pipe 48 handles into `marker info --json` and parse `fields` with a script, and `formattedValue` is a string such as `"100.0%"`.
- What the output could show: the marker's main payload value in the list row (`cpuPercent 100.0%`), or a min/max/mean of `cpuPercent` over the zoomed range.

## Question: "which other tests were running at time t?" (review-test_bookmark_abort_merging.js)

- Command: `profiler-cli thread markers --search "name:test" --list --limit 0` on a resource-usage profile.
- Got: every `test` marker overlapping the view, with starts rounded to the second (`t=17m30s`), so which ones span a given instant could not be read. I used `marker info --json` on each handle and a script.
- What the output could show: an `--at <t>` filter that keeps markers spanning that instant, or sub-second starts and ends in the row.
