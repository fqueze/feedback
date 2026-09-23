## Question: how far apart in time are two markers less than a millisecond apart?

- Command: `profiler-cli thread markers --session <s> --list --limit 0` after `zoom push 2.9424,2.9436` (a 1.2 ms view).
- Expected: start times at a resolution suited to the view (e.g. `t=2942.406ms`), so the gaps between the INFO, GCMinor, RDP Actor/Front and TEST-UNEXPECTED-FAIL markers can be read directly.
- Got: every row printed as `t=2.942s` / `t=2.943s` / `t=2.944s`; the sub-millisecond ordering and gaps, which were the whole question, were invisible.
- Workaround: `marker info m-A m-B ... --json` and `thread markers --list --json` piped through a Python script to print `start` (and `duration`, since flat-list JSON has no `end`).

## Question: which marker index does this handle have? (review-test_console_timestamp.html)

- Command: `profiler-cli marker info m-19 --session <s>`, to check a link's `marker=N`, which is what the review brief asks for.
- Expected: the marker index in the default output.
- Got: no index in the default output. `--json` has `markerIndex`, so each check needed a script over the JSON.
- Also hit the sub-millisecond `t=2.942s` resolution issue above, and used the same workaround.
