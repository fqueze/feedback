## `thread markers --list` timestamps too coarse on a long profile

- Command: `profiler-cli thread markers --category Test --search test_valuechange --list --limit 0 --session tvc-1` on an 11-minute profile.
- Expected: timestamps precise enough to order markers and to pick a `zoom push` range (ms resolution, e.g. `t=339.700s`).
- Got: every row printed as `t=5m40s`; 60 markers across ~140 ms all show the same time, so the list cannot say how far apart steps were.
- Workaround: `marker info <handles> --json` and read `start`.

## (review) Which `marker=N` in a profiler link is this marker?

- Question: checking a report's links, does marker m-62 have the `markerIndex` its link carries, and at what time (ms)?
- Command: `profiler-cli marker info m-62 --session review-test_valuechange.html-3`
- Expected: the marker index (the `marker=N` of profiler.firefox.com links) and a ms-precise start, in the default output.
- Got: neither; `Time: 5m37s - 5m37s (2.700ms)` and no index. Checking 5 links per profile took `marker info m-a m-b ... --json` piped through a Python one-liner printing `markerIndex` and `start`.
- Workaround: that script.
