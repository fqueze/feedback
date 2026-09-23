## Default session directory not writable under the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=browser_usercharacteristics_math.js profiler-cli load '<taskcluster url>' --session browser_usercharacteristics_math.js-1`
- Expected: session starts.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, then `Unknown session ... no metadata found`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every `profiler-cli` and `profile-link.py` call (the error message suggested it, which helped). Cost: one retry and a wrapper script.

## Question: in what order did events within the same second happen?

- Command: `profiler-cli thread markers --search 'firefox_binary_arch,...' --list` and `profiler-cli marker info m-859 m-11 m-56`
- Needed: whether `characteristics.firefox_binary_arch` was set before the `TEST-UNEXPECTED-FAIL` that read it as null, and where `Ping::submit` fell. All three print `t=2m35s` / `t=2m36s` (rounded to the second past one minute), so their order is not readable.
- Workaround: `marker info ... --json` and a script printing `start` in ms (155193.6 set, 155716.3 done notification, 155717.9 fail, 155719.9 submit).
- What the output could show: millisecond times (e.g. `t=155.194s`) in `--list` and `marker info`, at least when rows in the list share a rounded second.

## (review) Question: what did the harness log between browser launch and the first test?

- Command: `profiler-cli zoom push 790.0,792.5` then `profiler-cli thread markers --list --limit 0` on a `profile_resource-usage.json` (thread t-0, mach).
- Needed: the harness's own lines (`output`, `INFO`, `test`) around the first test of a manifest, to place Marionette's `browser-idle-startup-tasks-finished` against the Glean error.
- Got: 181 rows in 2.5 s, most of them `CPU Use` / `Memory` / `IO` / `NetIO` / `Sampling Interval` resource markers every ~100 ms.
- Workaround: pipe through `rg -v 'CPU Use|Memory  |  IO  |NetIO|Sampling Interval'`. `--search` with exclusions would have needed knowing each marker's field names first.
- What the output could show: resource-sampling markers hidden from `--list` by default on resource-usage profiles, or a `--no-resource` flag.

## (review) `marker info --json` returns a different shape for one handle and for several

- Command: `profiler-cli marker info m-57 m-63 --json` vs `profiler-cli marker info m-63 --json`
- Expected: the same shape either way.
- Got: `{"type":"marker-info-multi","markers":[...]}` for several handles, a bare marker object for one, so the same script breaks on the other case. Cost one retry.
- What the output could show: always a list, or always the `-multi` wrapper.
