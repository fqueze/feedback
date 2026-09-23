## Question: "at what millisecond did these markers happen, late in a long profile?"

- Command: `profiler-cli thread markers --search DOMEvent --list --limit 0 --session <s>` (and `profile markers --search ...`) on a 5m7s profile.
- Expected: start times precise enough to order markers a few ms apart (e.g. `t=307.357s`).
- Got: every row printed as `t=5m7s`, so the order of `UnsuppressAndInvalidate`, `mouselongtap` and `PBrowser::Msg_ChildToParentMatrix` within the same second could not be read. The `--json` `flatMarkers[].start` had the precision, but not the row's description text (`mouselongtap - document`), so both outputs had to be combined.
- Workaround: `--json` piped to a python one-liner printing `start/1000` with 3 decimals, plus narrow `zoom push` windows.
- What could have shown it: print seconds with millisecond precision (`t=307.357s`) whenever the profile is longer than a minute, or at least inside a zoom narrower than a second.

## Session dir not writable under the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster url> --session <s>`
- Expected: session starts.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the message suggested `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`, which worked (and is what other agents on this machine use).
- Workaround: export `PROFILER_CLI_SESSION_DIR` on every call. `profile-link.py` reads the same variable, so it works once set. The brief does not mention the variable.

## `--search "name:test"` also matched `TEST-PASS`

- Command: `profiler-cli thread markers --category Test --search "name:test,INFO,TEST-UNEXPECTED" --list --limit 0`
- Expected: the `test` interval markers (one per test), plus INFO and failures.
- Got: every `TEST-PASS` too (substring, case-insensitive match on the name), burying the per-test rows.
- Workaround: `--json` and filter `name == "test"` in python.
