## `--search` on samples/functions finds nothing in the full view, but matches in a zoomed view

- Session: profile https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/NMbnCN1zQtmQXT0c9L3UoA/runs/0/artifacts/public/test_info/profile_0_3069.json, thread t-0 (GeckoMain), 8279 samples of which 8207 have `GleanString.testGetValue` in the stack.
- Commands, no zoom:
  - `profiler-cli thread samples-top-down --include-idle --search testGetValue` -> "No samples matched"
  - `profiler-cli thread samples --include-idle --search GleanString` -> "No samples matched"
  - `profiler-cli thread functions --limit 0 --include-idle --search GleanString` -> "0 functions (filtered from 102)"
- After `zoom push 3.18,407` the same `samples-top-down --search testGetValue` returns the 100% tree.
- Expected: the search to match in the full view too (the samples are inside it).
- Also: `thread functions --limit 0` reports exactly "100 functions" on this thread, although handles up to f-2665 appear in the call tree; looks capped.
- Cost: made me believe for a while that initializeFOG / StartupTelemetry frames were absent from samples for a tool reason rather than a real one; I only trust the marker evidence for that.

## (review) `profile-link.py` refuses a session loaded from a profiler.firefox.com link

- The review brief says to check links with `profiler-cli load <link>`, which works and opens the link's thread.
- Then `PROFILER_CLI_SESSION_OWNER=review-… python3 profile-link.py --session review-…-1 --marker m-6186` -> "A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link." (exit 1)
- Expected: a `from-url/` link carries the raw artifact URL, so the session (or `profiler-cli status`) could record it and profile-link.py could reuse it.
- Workaround: stop the session and load the Taskcluster URL again (~1 extra profile load, all handles renumbered, markers re-found by search).
