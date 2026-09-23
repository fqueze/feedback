## `load` of a large profile: daemon dies silently, `load` exits 0 with no message

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli PROFILER_CLI_SESSION_OWNER=test_webtransport_stop_sending.js profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Stx10vOhTnauNpMnw9gncA/runs/0/artifacts/public/test_info/profile_test_webtransport_stop_sending.js.json" --session test_webtransport_stop_sending.js-1`
- Expected: the profile loads, or an error saying why it cannot (too large, out of memory).
- Got: `Loading profile from ...` then exit 0 after ~2 minutes, with no success or error line. The daemon log ends at `Fetching profile from ...`, with no error; the socket disappears (`Session ... is not reachable. The daemon exited without cleaning up`). Kept alive from a background shell, it still died ~8.5 minutes in, silently. The artifact is 60 MB gzipped, 868 MB decompressed (measured with curl), above V8's ~512 MB maximum string length, which is my guess at the cause.
- Also: when the `load` client gives up (~2 min) while the daemon is still fetching, the daemon dies with the calling shell, so a slow download can never finish from a foreground call. Other sessions in the same directory survived a 2m45s fetch, so this may be sandbox-specific.
- Workaround: none for this profile; read a smaller one.
- Cost: four load attempts, ~20 minutes.

## Default session directory not writable in this sandbox

- Command: any `profiler-cli load ... --session ...` without `PROFILER_CLI_SESSION_DIR`.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a helpful hint to set `PROFILER_CLI_SESSION_DIR`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command. The brief does not mention it.

## `thread info` sample count ignores the zoom

- Command: `profiler-cli zoom push 0,0.7 --session test_webtransport_stop_sending.js-2` then `profiler-cli thread info --session test_webtransport_stop_sending.js-2`, repeated for several ranges.
- Expected: the number of samples in the zoomed range (to see whether the sampler kept its 10 ms rate).
- Got: `This thread contains 559 samples and 3007452 markers.` for every range, while the header shows the zoomed view.
- Workaround: none tried; counted the rate over the whole profile instead.

## `thread markers --group-by field:name --json` has no obvious per-group count

- Command: `profiler-cli thread markers --session test_webtransport_stop_sending.js-2 --search name:Runnable --group-by field:name --json`
- Wanted: per-window counts of `DummyEvent` runnables. A generic walk for `{key, count}` found nothing; switched to the text output and grepped `markers (`.
