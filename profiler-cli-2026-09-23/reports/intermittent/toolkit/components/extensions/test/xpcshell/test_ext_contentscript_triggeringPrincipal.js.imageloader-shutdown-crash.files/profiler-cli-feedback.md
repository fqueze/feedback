## Question: "in which order did these log lines happen?" (resource-usage profile, 10 min long)

- Command: `profiler-cli thread markers --session <s> --search triggeringPrincipal --list --limit 0` on the resource-usage profile of VmPHw38kTMqj4FMHuBDI7g.
- Expected: timestamps precise enough to order the replayed log lines, which are 1 ms apart.
- Got: `t=1m10s` / `t=1m11s`. That is 1 s resolution, so 60 lines looked simultaneous.
- Workaround: `--json` and sort on `start`, which is ms-precise.

## `✓ has stack` on replayed `C++ warning` markers, but the stack is empty

- Command: `profiler-cli marker stack m-317 --session <s>` (resource-usage profile, `C++ warning '!mCancelable'`)
- Expected: a stack, since the list marks it `✓`.
- Got: `[1] unknown!null`.
- Could show: no `✓` for these, or say the stack is empty.

## (review) Session names from the recommended `<owner>-N` scheme overflow the socket path

- Command: `profiler-cli load <url> --session review-test_ext_contentscript_triggeringPrincipal.js.imageloader-shutdown-crash-1`
- Expected: a session; report names are this long by design.
- Got: `The Unix socket path for this session is 114 bytes, over this platform's 107-byte limit`.
- Workaround: a shortened session name (`review-tp-imageloader-N`). Could hash long names into the socket path.

## (review) Question: "what was the machine's CPU use, min/max/mean, over 68–72 s?" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "CPU Use" --list --limit 0 [--json]` after `zoom push 68,72`.
- Expected: each marker's `CPU Percent`, or a min/mean/max summary for the range.
- Got: names and durations only; `--json` has no payload either, so it took `marker info <every handle> --json` and a script.
- Could show: the `CPU Use` percent in the list row, or stats in the aggregate view.
