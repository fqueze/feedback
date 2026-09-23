# Resource-usage profile feedback

Problems specific to CI resource-usage profiles (`profile_resource-usage.json`), from the same feedback files. Sources for each item: [resource-usage-profile-feedback-sources.md](resource-usage-profile-feedback-sources.md).

# Using resource-usage profiles with profiler-cli

## 1. Resource-usage profiles: "No counters" and "No significant activity" despite CPU data (324)

On a resource-usage profile, `profile info` and `counter list` report no CPU data, though machine CPU exists as 14,932 `CPU Use` interval markers (`cpuPercent`, `idle_pct`) that neither command mentions.

- **Question:** How busy was the machine when this test failed?
- **Command:** `profiler-cli counter list --session bcwp-ru3`
- **Expected:** A machine CPU track ("the CPU use of the whole machine").
- **Got:** "No counters in this profile." and, from `profile info`, "CPU activity over time: No significant activity."
- **Suggestion:** Have `profile info` summarise the `CPU Use` markers the way it does CPU samples, or expose them as a counter.
- **Example from:** [mac/docshell/test/browser/browser_browsingContext-webProgress.js.files/profiler-cli-feedback.md](reports/mac/docshell/test/browser/browser_browsingContext-webProgress.js.files/profiler-cli-feedback.md)

## 2. CPU Use / IO / Memory rows listed without their values (241)

On a resource-usage profile, `thread markers --search "name:CPU Use" --list` rows show only name, time and duration, never the cpuPercent value, so machine CPU over a range needed `marker info` per marker or `--json` plus a script.

- **Question:** What was the machine's CPU use, second by second, over this range?
- **Command:** `profiler-cli zoom push 50,80 --session <s>; profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session <s>`
- **Expected:** Each CPU Use row showing its CPU Percent, or a per-bucket summary of it.
- **Got:** Rows with only name, time and duration: `m-185 CPU Use t=49.966s 100ms`.
- **Suggestion:** Print the marker's main field (cpuPercent) in list rows, or summarise it (min/avg/max) over the zoom.
- **Example from:** [devtools/devtools/client/debugger/test/mochitest/browser_dbg-audiocontext.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/debugger/test/mochitest/browser_dbg-audiocontext.js.files/profiler-cli-feedback.md)

## 3. cpuPercent and similar fields are strings in JSON (20)

In `thread markers --search "CPU Use" --list --json` on a resource-usage profile, `data.cpuPercent` is a formatted string like `"30.8%"`, so numeric comparisons in scripts fail until the `%` is stripped.

- **Question:** What was the machine's CPU use during the test's wait?
- **Command:** `profiler-cli thread markers --session review-AutocompleteTest.kt.bug1808064-1 --search "CPU Use" --list --limit 0 --json`
- **Expected:** `data.cpuPercent` as a number.
- **Got:** A string such as `"30.8%"`; numeric comparison failed until `float(v.rstrip('%'))`.
- **Suggestion:** Provide a numeric raw value next to the formatted one, or a --stats field:cpuPercent summary.
- **Example from:** [intermittent/mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/AutocompleteTest.kt.bug1808064.files/profiler-cli-feedback.md](reports/intermittent/mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/AutocompleteTest.kt.bug1808064.files/profiler-cli-feedback.md)

## 4. Partial (streamed) resource-usage profile can't be loaded (14)

`load` of a max-run-time task's resource-usage profile (unfinalized JSONL stream) fails with a bare JSON parse error, and the failed load leaves a live daemon session behind.

- **Question:** Was the machine busy or idle while the job hung until it was killed?
- **Command:** `profiler-cli load "<url>/profile_resource-usage.json" --session 2073842-1`
- **Expected:** The job timeline (CPU Use, Phase markers) loaded; no session left after a failure.
- **Got:** `Error: Profile load failed: Unserializing the profile failed: SyntaxError: Unexpected non-whitespace character after JSON at position 11668 (line 2 column 1)`; daemon still in `session list`.
- **Suggestion:** Load the line-delimited stream (or name the format in the error), and stop the daemon on load failure.
- **Example from:** [intermittent/bugs/2073842.files/profiler-cli-feedback.md](reports/intermittent/bugs/2073842.files/profiler-cli-feedback.md)

## 5. Resource-usage marker types clutter lists (4)

On resource-usage profiles, `thread markers --list` in a zoom is dominated by `CPU Use`/`Memory`/`IO`/`NetIO`/`Sampling Interval` markers (1530 of 1567), burying harness log lines; hiding them needs five exclusions or grep.

- **Question:** What did the harness do during this test, in order?
- **Command:** `profiler-cli thread markers --session rsa-ssi-1 --list --limit 200`
- **Expected:** The test's harness log lines.
- **Got:** CPU Use / Memory / IO / NetIO / Sampling Interval markers every 100 ms made up 1530 of 1567 markers.
- **Suggestion:** Hide resource-sampling marker types by default on resource-usage profiles, or add a `--no-resource` flag.
- **Example from:** [intermittent/testing/web-platform/tests/storage-access-api/requestStorageAccess-same-site-iframe.sub.https.window.js.files/profiler-cli-feedback.md](reports/intermittent/testing/web-platform/tests/storage-access-api/requestStorageAccess-same-site-iframe.sub.https.window.js.files/profiler-cli-feedback.md)

# Resource-usage profile data

## 6. Resource-usage profiles store machine CPU only as markers, not as a counter (41)

Machine CPU/memory/IO exists only as thousands of `CPU Use`/`Memory`/`IO` interval markers (one per ~100 ms). There's no counter track, so tools and the front-end report "no CPU activity".

*Suggestion:* Emit a CPU (and memory/IO) counter track in resource-usage profiles.

## 7. Killed tasks leave an unfinalized, unloadable resource-usage profile (12)

When a task is killed (max run time, memory guard, failed build), the resource-usage profile stays in its streamed JSON-lines form and can't be loaded. The most interesting profiles are lost.

*Suggestion:* Finalize the profile on kill, or make the loader accept the streamed form.

## 8. `CPU Use` marker cpuPercent stored as a string (10)

`cpuPercent`, `idle_pct` etc. are stored as formatted strings (`"98.9%"`) instead of numbers, unlike other numeric fields.

## 9. Tests started but never ended get no test marker (7)

The resource monitor emits the `test` marker only at test_end, so a test that timed out or crashed (the interesting one) has no marker at all.

## 10. Some resource-usage profiles contain no machine CPU data at all (4)

macOS and Windows xpcshell resource-usage profiles sometimes have no counters, no CPU Use markers and a single thread at 0 ms CPU.

## 11. `CPU Use` marker fields inconsistent with each other (3)

User% + Idle% exceeds 100%, System% is 0 while raw system is non-zero, and raw user/system/iowait fields hold stacked values.

