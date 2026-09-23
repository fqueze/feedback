## `thread markers --list` prints multi-line marker text in full

- Command: `profiler-cli thread markers --category Test --search browser_newtabButton --list --limit 0 --session ...`
- Expected: one line per marker, with long/multi-line payloads cut (as other columns are), so the test log stays readable.
- Got: one `INFO` marker ("Restoring to browserState: {...}", a pretty-printed JSON of 50 tabs) printed as ~400 lines in the middle of the list.
- Workaround: pipe through `rg -v '^\s+("|\{|\}|\]|\[)'`.

## (review) `--session` before the subcommand is rejected

- Command: `profiler-cli --session browser-review-browser_newtabButton.js-1 zoom push 2.95,3.12`
- Expected: a global option that works before or after the subcommand, like `git -C`.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand's arguments.

## (review) Question: "when was this runnable queued, and by which task?"

- Command: `profiler-cli thread markers --search 2cc5f68c4f8642d00 --list --limit 0 --session ...` (the `task:` id of a `NotifyVsyncOnMainThread[low priority]` Runnable at 3.018 s)
- Expected: the `Runnable` marker, or `marker info` on it, giving the `TaskController::AddTask` that queued it, with its time and the runnable running then.
- Got: the id is a reused pointer, so the search returns several `AddTask`/`Runnable` pairs for different tasks. Which `AddTask` belongs to this one (2.918 s, 100 ms earlier) had to be worked out by hand from the order of the pairs.
- What would have answered it: `marker info` on a Runnable showing "queued at t=…, during <enclosing Runnable>", from the matching flow marker just before it.
