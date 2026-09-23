# fx-tests feedback — test_errorhandler_1.js

## The failing xpcshell test's stdout is in the resource-usage profile, and nothing says so

- Command: `fx-tests task WgINS7cnSseqypcayKXhjA --profiles`
- Question: what did the test print (here `print("Failure in ping validation callback", ex)` and the Sync logs), which the per-test profile does not hold?
- Expected: a hint next to the resource-usage profile URL that it holds the replayed log of each failing xpcshell test as `output` markers.
- Got: only the URL. I found the `output` markers by chance, after reconstructing the same timestamps by hand from network markers and a wall-clock mapping in the per-test profile; the replayed log gave the answer directly (`ping.status is undefined`, `Updating collection crypto to 1790027792.79`).
- Workaround: `profiler-cli thread markers --search "<text>" --list` on the resource-usage profile.
