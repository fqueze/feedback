## The crash signature of a manifest-attributed crash in a job (browser_ml_utf8_codepoint.js)

- Command: `fx-tests task L_hUPhc6RRyZ_Ak5F4JCAA --profiles` (and six other jobs)
- Question: which process crash followed my test's TIMEOUT in this job, and with what signature.
- Got: `warning: 1 failing marker in this job named no test path and is not in the table below ... CRASH toolkit/components/ml/tests/browser/browser.toml` — the signature is not shown, and `--messages` / `--json` do not carry it either. `fx-tests crashes` has no row for it (no test path).
- Workaround: load each job's resource-usage profile in profiler-cli and search `CRASH` markers (7 loads). The warning line could print the signature and the time, e.g. `CRASH @ mozilla::MozPromise<...>::ThenValueBase::AssertIsDead — browser.toml, 92 s after browser_ml_utf8_codepoint.js TIMEOUT`.

## When did the test's duration change (browser_ml_utf8_codepoint.js)

- Command: `fx-tests test <path> --durations` then 16 calls of `--durations --day <d> --config macosx`
- Question: since when does the test take 44 s instead of 0.3 s on macOS.
- Got: `--durations` over the whole window (median 340 ms, p95 44,548 ms) hides a step change; `--history` has per-day pass/fail counts but no durations.
- Workaround: loop `--durations --day` per day. A per-day median duration column in `--history` (or `--durations --history`) would have shown the step on 2026-09-02 and back on 2026-09-10 in one call.

## Per-day run/fail counts for one config family

- Command: `fx-tests test <path> --config macosx --day <d> --json` in a loop, reading `totals.runCount/failCount/timeoutCount`.
- Question: the failure rate on macOS inside the 2026-09-03..09 window.
- Got: `--history` does not honour a per-config breakdown in its text output as far as I could tell; needed JSON per day.
- Workaround: script. `--history --config macosx` printing its own rows would answer it.

## `--bugs` prints nothing when there is no bug

- Command: `fx-tests test <path> --bugs`
- Expected: a line such as `Bugs: none annotated`.
- Got: no Bugs section at all in text output (`--json` has `"annotatedBugs": []`), which reads like the flag was ignored.

## Correction to "Per-day run/fail counts for one config family" above

- `fx-tests test <path> --history --config macosx` does print per-day counts for the filtered configs. That entry was my mistake, not a tool gap; only the per-day duration request stands.
