## `load` of a large profile: daemon dies silently, twice

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli PROFILER_CLI_SESSION_OWNER=test_ext_contentscript_context_isolation.js profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/JOHAJGSXRpuFDgZuNJnKSg/runs/0/artifacts/public/test_info/profile_test_ext_contentscript_context_isolation.js.json" --session test_ext_contentscript_context_isolation.js-1`
- Expected: the profile loads, or an error saying why not (too large, out of memory).
- Got: `Error: Session ... is not reachable. Nothing is accepting connections on ....sock. The daemon exited without cleaning up.` The daemon log ends at `Fetching profile from ...` with no error. A retry did the same, and the second time `load` printed nothing at all after `Loading profile from ...`; the session just vanished from `session list`.
- Cause found by hand: the artifact is 53 MB gzip, 896 MB decompressed JSON, above V8's maximum string length (~512 MB), so it presumably cannot be parsed as one string. Other macOS VM per-test profiles of this test are 30-56 MB gzip.
- Workaround: none within profiler-cli; picked smaller profiles (Linux ones are ~1 MB gzip). Cost: 4 calls and a 900 MB download to find out.
- Wish: log the fetch/decompress/parse failure (or the size) to the daemon log and to `load`'s output; ideally stream-parse large profiles.
