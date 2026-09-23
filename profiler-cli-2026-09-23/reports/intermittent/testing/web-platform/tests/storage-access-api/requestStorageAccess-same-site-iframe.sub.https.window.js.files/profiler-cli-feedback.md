# profiler-cli feedback (requestStorageAccess-same-site-iframe.sub.https.window.js)

## Question: "what did the harness do during this test, in order, with sub-second spacing?"

- Command: `profiler-cli thread markers --session rsa-ssi-1 --list --limit 200` after `zoom push m-1` on a wpt resource-usage profile.
- Expected: the test's harness log lines.
- Got: `CPU Use` / `Memory` / `IO` / `NetIO` / `Sampling Interval` markers, one set every
  100 ms, made up 1530 of the 1567 markers in the range. A negative-only `--search` was
  not obvious; I used a positive OR list (`DEBUG,INFO,…,test`) and then grepped them out.
- The `--list` time column has one-second resolution (`t=19m18s`). The first bless click
  and the second one, 56 ms apart, looked simultaneous, and the gap was the whole
  finding. I switched to `--json` and printed `start` myself.
- What could have shown it: millisecond times in `--list` when the range is under a
  minute, and a way to hide the resource-usage marker types by default on this profile type.
