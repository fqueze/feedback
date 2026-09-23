## Question: how many ms after its start did this test give up? (`thread markers --list` rounds times to seconds)

- Command: `profiler-cli thread markers --session test_promise_state-02.js-1 --search promise_state-02 --list --limit 0` on the LKys8j8iTQKTAN9hKv040g resource-usage profile (3m37s long).
- Expected: start times precise enough to compare two markers 40 ms apart (the `test` TIMEOUT marker and its `failed or timed out, will retry.` INFO).
- Got: both printed as `t=2m13s`. Same for the first `Failed to launch` warning (`t=2m12s`), so "how long after the break did this test start" could not be read either.
- Workaround: `--json` and a Python one-liner over `flatMarkers[].start`. Printing ms (e.g. `t=2m13.400s`) once the profile is over a minute long would have answered it.

## Question: which instant markers fall in a 3 ms window?

- Command: `profiler-cli zoom push 178.442,178.445` then `thread markers --list --limit 0`.
- Expected: the few instant log lines inside the window (the replayed full log of the test).
- Got: 350 markers, first all the long interval markers overlapping the window (`Phase run-tests`, `parallel`, hundreds of 45 s `test` TIMEOUT markers), so the instant lines were beyond what I read.
- Workaround: `--search promise_state-02` without zoom. An `--instant` / `--starts-in-range` filter would have answered it.
