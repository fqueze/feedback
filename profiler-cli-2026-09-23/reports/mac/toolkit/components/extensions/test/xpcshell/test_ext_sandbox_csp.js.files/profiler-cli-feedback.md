## Question: "what was the machine's CPU use while this test ran?"

- Command: `profiler-cli zoom push m-1` (the `test` marker in `profile_resource-usage.json`), then `profiler-cli thread markers --search "CPU Use" --list --limit 0`.
- Expected: some summary of the `cpuPercent` field over the zoomed range (mean, min, share at 100%), since the resource-usage profile has no counter to feed `counter info`.
- Got: 369 rows each saying `CPU Use 99ms`, with the percentage only visible through `marker info` one marker at a time.
- Workaround: `--json` and a script averaging `flatMarkers[].fields[key=cpuPercent]` (`cpu.py` here). `thread markers` could show numeric-field stats per marker name, or the resource-usage CPU could be exposed as a counter.

## Session commands fail silently when `PROFILER_CLI_SESSION_DIR` is unset in a sandbox

- Command: `profiler-cli load <url> --session test_ext_sandbox_csp.js-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a clear hint. Fine, but the follow-up `profile info` call in the same shell then printed "Unknown session", which reads as a different problem. Minor.
## `thread samples` after `zoom clear` returns the previous zoom's result

- Session: `profile_test_ext_sandbox_csp.js.json` of task BtgCEFeRS5uC3PNocCIgnQ (Windows), thread t-0, 3540 samples per `thread info`.
- Commands: `zoom push 36.22,61` → `thread samples --include-idle` gives `Categories (1480 running samples)`. Then `zoom clear` (status: "View range: Full profile") → `thread samples --include-idle` gives the same `1480 running samples` and the same percentages. Same with `zoom push m-6` then `zoom clear`: 1503, the m-6 figure.
- Expected: the full-profile figures (about 3510 samples: 527 + 1503 + 1480 over the three sub-ranges).
- Workaround: never read samples unzoomed after a zoom; zoom explicitly to the full range, or sum sub-ranges. This silently gave me wrong whole-profile percentages at first.

## Question: "how long does one runnable take in every content process, and what runs right after it?"

- Commands: `thread select t-N` then `thread markers --search "Msg_SetProcessSandbox,Msg_ConstructBrowser" --list --json`, looped over 21 content-process threads, with a script computing `ConstructBrowser.start - (SetProcessSandbox.start + duration)`.
- `profile markers --search X` says which threads have the marker, but not each occurrence's start and duration. A cross-thread `--list` (one row per occurrence, with its thread) would have answered this without a loop over `thread select`.

## Review: a calltree link's `search=` silently filters every later `thread samples` call

- Session: `profiler-cli load "<profiler.firefox.com link to profile_test_ext_sandbox_csp.js.json of task TAOJNTmcQCiDti01vVH5MA>/calltree/?range=8810m14290&thread=0&search=getBaselinePrefs&v=17"`.
- Commands: `thread samples --include-idle`, then `zoom pop`, `zoom push 8.811,23.101`, `thread samples --include-idle` again.
- Expected: all 1203 samples of the range, as a load of the raw Taskcluster URL then gives.
- Got: `Categories (447 running samples)` every time, meaning only the samples matching `getBaselinePrefs`, with no `Search:` line in the output. `status` said `Filters: none` and `filter list` said `No active filters for t-0`, so nothing showed that the URL's search was still applied.
- Workaround: reload from the raw artifact URL. The review brief says to check a link by loading it, so any percentage read that way is wrong without warning.
