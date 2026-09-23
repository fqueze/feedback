## Session name derived from the report name is too long for the socket path

- Command: `profiler-cli load <taskcluster url> --session test_RSEL_updateRecipes_RemoteSettingsClient_get.js.child-hang-shutdown-1`
- Expected: a session, as the brief asks for sessions named `<report name>-1`.
- Got: `The Unix socket path for this session is 107 bytes, over this platform's 103-byte limit`.
- Workaround: a short session name (`rsel-get-hang-1`). The brief's naming rule plus long test file names routinely exceeds the macOS limit; hashing or truncating the socket file name (not the session id) would avoid it.

## `~/.profiler-cli` not writable in the agent sandbox

- Command: `profiler-cli load <url> --session rsel-get-hang-1`
- Got: `Cannot create the profiler-cli session directory /Users/florian/.profiler-cli ... EPERM`; the message suggested `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`, which worked. Clear message; noting it only because `profile-link.py` also needs the same variable set, which is easy to miss.

## "Was the machine saturated during this window?" on a resource-usage profile

- Command: `profiler-cli profile info` / `counter list` / `thread info` on `profile_resource-usage.json` of an xpcshell job.
- Expected: machine CPU over the current zoom, which the brief says this profile holds.
- Got: "CPU activity over time: No significant activity", "No counters in this profile", "0 samples": all read as "no CPU data". The CPU is in 18,361 `CPU Use` interval markers with a `cpuPercent` field, and nothing summarizes them.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and a Python script averaging `data.cpuPercent` per second. A `profile info` line for resource-usage profiles ("machine CPU: mean 96%, 93-100% over this zoom") would have answered it.
