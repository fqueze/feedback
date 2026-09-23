## Ordering events within one second of a long profile

- Question: "did the permitUnload SpinEventLoop end before or after the test's waitForEvent listener ran, and did the PollIdleTime spin start before DOMModalDialogClosed?" — all within ~5 ms.
- Command: `profiler-cli thread markers --search 'SpinEventLoop,DOMModalDialogClosed,waitForEvent/' --list --limit 0 --session <s>` on a 5m55s profile.
- Expected: start (and end, for intervals) with millisecond precision.
- Got: `t=4m27s` for every row, and a duration; the order within the second is only the row order, and interval ends are not shown at all.
- Workaround: `--json` piped to a script printing `start` and `start+duration` in ms. Needed on every profile of this investigation.
- What the default output could show: `t=266.689s` (or `4m26.689s`) once the profile is longer than a minute, plus the end time for interval markers.
