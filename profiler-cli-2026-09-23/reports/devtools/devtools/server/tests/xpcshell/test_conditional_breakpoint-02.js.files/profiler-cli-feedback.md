## Marker times after the first minute are rounded to the second

- Command: `profiler-cli thread markers --search conditional_breakpoint-02 --list --limit 0 --session test_conditional_breakpoint-02.js-2` (LKys8j8iTQKTAN9hKv040g).
- Question: how long after its start did this test log `will retry`, and how long after the first launch failure did it start?
- Expected: `t=2m13.095s` and `t=2m13.129s`, as markers before 1 minute get (`t=46.435s`).
- Got: `t=2m13s` for both, and `t=2m12s` for the launch failure, so 34 ms and 1.4 s cannot be told apart.
- Workaround: `marker info m-1 m-3 m-7 --json` through a Python one-liner to read `start`. That JSON has no `handle` field, so a multi-marker call has to be matched back to the handles by order.
