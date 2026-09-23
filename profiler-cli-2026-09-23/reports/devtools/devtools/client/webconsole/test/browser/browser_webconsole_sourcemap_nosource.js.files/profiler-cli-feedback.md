## `thread markers --list` prints `test` markers with an empty description

- Command: `profiler-cli thread markers --session S --category Test --search name:test --list --limit 0`
- Expected: each row shows which test it is and its status (the marker's label, e.g.
  `PASS — devtools/.../browser_webconsole_sourcemap_error.js`).
- Got: `m-1289  11.72  13.9` with an empty description. The label is only in `--json`.
- Question behind it: "which tests ran before mine in this browser session, and did they pass?".
  I had to script over the `--json`.

## `marker info` prints times to the second only

- Command: `profiler-cli marker info m-16 m-11 --session S`
- Got: `Time: 2m37s (instant)`. The ms timestamp (157226.157) is only in `--json`. Several markers
  in one second cannot be ordered from the text output.

## `thread markers --list` prints times to the second only (review)

- Command: `profiler-cli thread markers --session S --search 'name:Runnable,name:Perform microtasks' --list --limit 0`
- Question: in what order, and how far apart, did `WorkerFetchResponseEndRunnable`, `ReleaseRefControlRunnable`, `ContinueConsumeBodyRunnable` and `Perform microtasks` run on one worker?
- Got: every row `t=2m37s`, so rows within one second cannot be timed against each other or against the main thread. The ms start is only in `--json`; I scripted over it for every thread I read.
- It could show: ms (or µs, relative to the zoom start) in the list rows once more than one row falls in the same second.
