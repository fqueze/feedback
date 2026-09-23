## Which of two markers in the same millisecond, on different threads, came first

- Question: did the parent send the first touch to APZ (parent main thread, `PAPZInputBridge::Msg_ReceiveMultiTouchInputEvent`) before or after the content process sent its first display list (`ForwardDPTransaction`) and the GPU compositor received it (`SetDisplayList`)? All three happen at "t=4.733s".
- Command: `thread markers --list --search ...` on each thread, with a 6 ms zoom in place.
- Got: every row says `t=4.733s`, so the order can't be read. I had to run `--json` and a python snippet to print `start` with sub-millisecond precision (4732.578 vs 4732.985 vs 4733.050).
- Would have helped: more digits in `t=` when the zoom is only a few ms wide (or a `--precise` flag). Better still, a cross-thread chronological list (`profile markers --search A,B,C --list`, sorted by start) at that precision.

## Default session directory not writable under a sandbox

- Command: `profiler-cli load <url> --session ...` with no PROFILER_CLI_SESSION_DIR set.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The message suggested setting PROFILER_CLI_SESSION_DIR, which worked. That variable then has to be passed to `profile-link.py` too; the brief does not mention it.

## `marker info --json` changes shape with the number of handles (review)

- Command: `profiler-cli marker info m-6 --json` vs `profiler-cli marker info m-1 m-2 --json`, `--session review-browser_autoplay_policy_touchScroll.js-6`.
- Expected: one shape, so one parser handles both.
- Got: a bare marker record for one handle, `{type, requested, markers: [...], errors, context}` for several. A script written for one broke on the other, twice.
- Workaround: branch on `'markers' in d`.
- Question it was for: "what is this marker's `start` to the microsecond, and its `markerIndex`", which the default output also doesn't answer (see the sub-millisecond entry above: this review needed it again, to order the GPU-side touch runnable against the content's `ForwardDPTransaction`).
