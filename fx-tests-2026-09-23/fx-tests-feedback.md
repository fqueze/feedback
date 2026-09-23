# fx-tests feedback

Aggregated from 1743 `fx-tests-feedback.md` files. Counts are the number of reports mentioning each item; each example is one real report. Gaps in the CI data fx-tests reads are in [ci-data-feedback.md](ci-data-feedback.md), sheriffing and Bugzilla data issues in [sheriffing-feedback.md](sheriffing-feedback.md), and problems with other services in [other-tools-feedback.md](other-tools-feedback.md). 27 reports about problems outside fx-tests aren't counted here. Sources for each item: [fx-tests-feedback-sources.md](fx-tests-feedback-sources.md).

## 1. `test --bugs` prints nothing when no bug matches the test (575)

`fx-tests test <path> --bugs` prints exactly the same output as without the flag when no sheriff-annotated bug names the test: no Bugs section and no "none found" line, so "no bug" looks like "flag ignored".

- **Question:** Which bugs track this test's failures?
- **Command:** `fx-tests test browser/modules/test/browser/browser_preloading_tab_moving.js --bugs`
- **Expected:** A Bugs section, or a line saying no sheriff-annotated bug names the test, ideally with open bugs naming it (bug 1720203).
- **Got:** Exactly the output without `--bugs`; no section and no "none found" line, so it reads the same as the flag being ignored.
- **Suggestion:** Always print a Bugs section, with "none" when empty, possibly listing open Bugzilla bugs whose summary names the test.
- **Example from:** [browser/browser/modules/test/browser/browser_preloading_tab_moving.js.files/fx-tests-feedback.md](reports/browser/browser/modules/test/browser/browser_preloading_tab_moving.js.files/fx-tests-feedback.md)

## 2. `test --bugs` omits the test's own "single tracking bug" and resolved bugs (189)

`fx-tests test <path> --bugs` printed the same output as without `--bugs`: no bug section, no "none found" line, although Bugzilla has an open tracking bug and several resolved bugs naming the test. The agent had to query Bugzilla by hand.

- **Question:** Which Bugzilla bugs name this test, including resolved ones?
- **Command:** `fx-tests test devtools/shared/commands/target/tests/browser_target_command_service_workers.js --bugs`
- **Expected:** Bug 1781324 ("... | single tracking bug", NEW) and the resolved bugs naming the test (1725052 dup, 1728689, 1734047).
- **Got:** The same output as without `--bugs`, with no bug section and no "none found" line either.
- **Suggestion:** Search Bugzilla summaries for the test path, include resolved bugs, and print an explicit "none found" line when empty.
- **Example from:** [devtools/devtools/shared/commands/target/tests/browser_target_command_service_workers.js.files/fx-tests-feedback.md](reports/devtools/devtools/shared/commands/target/tests/browser_target_command_service_workers.js.files/fx-tests-feedback.md)

## 3. `try --all-jobs --test` silently ignores `--task-ids` and `--profiles` (126)

`fx-tests try <rev> --all-jobs --test <path> --task-ids` printed only the per-config counts table. `--task-ids` was accepted but ignored, and `--json` had no task IDs either, so the jobs behind the failing config could not be found.

- **Question:** Which task IDs ran this test on the config where it failed?
- **Command:** `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/inspector/test/browser_inspector_textbox-menu.js --task-ids`
- **Expected:** The per-config table plus the task IDs behind each row, at least behind the `failed` column.
- **Got:** The table only (`a11y-checks-4 ... 3 jobs, 0 passed, 3 failed`), no task IDs; `--json` had none either.
- **Suggestion:** Make `--test` honour `--task-ids`/`--profiles`, printing the failing tasks and their per-test profile URLs under each row.
- **Example from:** [devtools/devtools/client/inspector/test/browser_inspector_textbox-menu.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/inspector/test/browser_inspector_textbox-menu.js.files/fx-tests-feedback.md)

## 4. `test --task-ids` shows no revision or push date per failing task (116)

`fx-tests test <path> --task-ids --limit 0` lists task ID, job name and run day only, in text and JSON. Three failures run on 2026-09-14 were on revisions pushed 2026-08-15, which looked like a recurrence after the fix until checked task by task.

- **Question:** Were all these failures on revisions pushed before a given fix landed?
- **Command:** `fx-tests test browser/components/extensions/test/browser/browser_toolbar_prefers_color_scheme.js --task-ids --limit 0`
- **Expected:** The repo and revision, ideally the push time, of each failing task, to place failures before or after a landing.
- **Got:** Task id, job name, day of the run only; no revision in text or JSON.
- **Suggestion:** Add a revision and push-time column to `--task-ids` (text and JSON).
- **Example from:** [mac/browser/components/extensions/test/browser/browser_toolbar_prefers_color_scheme.js.files/fx-tests-feedback.md](reports/mac/browser/components/extensions/test/browser/browser_toolbar_prefers_color_scheme.js.files/fx-tests-feedback.md)

## 5. `test` Issues ranks a TEST-KNOWN-FAIL a11y todo as the first failure (113)

`fx-tests test` picked a TEST-KNOWN-FAIL (a11y `handleEvent()` todo, logged via `SimpleTest.todo`) as each run's first failure. The real TEST-UNEXPECTED-FAIL, a timeout, never appeared in Issues, so the test looked like an a11y bug. `task --messages` also lists both under `FAIL`.

- **Question:** What is this test's most frequent real failure mode?
- **Command:** `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_prompts.js`
- **Expected:** Issue 1 to be `This test exceeded the timeout threshold ... limit was 90s`, the only TEST-UNEXPECTED-FAIL in the ASan runs.
- **Got:** `1. 645x FAIL handleEvent() was unable to perform a11y checks on hidden node: id: main-button`, a TEST-KNOWN-FAIL; the timeout message is not in Issues at all.
- **Suggestion:** Skip known-fail/todo results when picking the "first failure per run".
- **Example from:** [browser/browser/components/aiwindow/ui/test/browser/browser_smartwindow_prompts.js.files/fx-tests-feedback.md](reports/browser/browser/components/aiwindow/ui/test/browser/browser_smartwindow_prompts.js.files/fx-tests-feedback.md)

## 6. `test --task-ids` lists only failing tasks, no way to get passing runs (80)

`fx-tests test <path> --task-ids` lists only failing tasks and `fx-tests test --help` has nothing for passing ones, so the agent could not get passing task IDs of the same test to check whether a log line seen in every failing job was absent from passing ones.

- **Question:** Which jobs ran this test and passed it, on a given push or config?
- **Command:** `fx-tests test <path> --task-ids`
- **Expected:** A few passing task IDs of the same test (same push or same config).
- **Got:** Only the failing tasks. Workaround: Treeherder jobs API, then each task's `manifests.list` and `live_backing.log`, about 350 requests.
- **Suggestion:** Add `--passing-task-ids [--limit n]` next to `--task-ids`, or list passing tasks per config in `--coverage`.
- **Example from:** [mac/toolkit/components/resistfingerprinting/tests/browser/browser_usercharacteristics_svg.js.files/fx-tests-feedback.md](reports/mac/toolkit/components/resistfingerprinting/tests/browser/browser_usercharacteristics_svg.js.files/fx-tests-feedback.md)

## 7. `fx-tests task` has no `--test <path>` filter; truncated list hides the test (79)

In jobs with ~1000 failures, `fx-tests task` lists only the first ~20 failing rows alphabetically. There is no `--test <path>` filter, so the test the agent came from is hidden and needs `--limit 0` plus grep over thousands of lines.

- **Question:** What happened to my test (`test_require.js`) in this job with ~1000 failures?
- **Command:** `fx-tests task JOcVulCfRyynVd-aExDW1g --profiles`
- **Expected:** A way to see the row for `devtools/shared/tests/xpcshell/test_require.js`.
- **Got:** The first 18 failing rows alphabetically, then `… 1041 more (--limit 0 for all)`; my test was not among them.
- **Suggestion:** Add a `--test <path>` filter to `task`, or a one-line summary for mass-failure jobs (e.g. "1057 TIMEOUTs, all started between t=45s and t=55s").
- **Example from:** [devtools/devtools/shared/tests/xpcshell/test_require.js.files/fx-tests-feedback.md](reports/devtools/devtools/shared/tests/xpcshell/test_require.js.files/fx-tests-feedback.md)

## 8. No co-failure view: which other tests failed in this test's failing jobs (68)

To check whether a suspected leaker failed before a victim in every job, the agent ran `fx-tests test <path> --task-ids --limit 0` then one `fx-tests task <id> --profiles --limit 0` per failing task (30 calls), grepping each. No command reports co-failures.

- **Question:** Did the same earlier test fail in every job where this one failed?
- **Command:** `fx-tests test <path> --task-ids --limit 0`
- **Expected:** A co-failure count, e.g. "browser_ext_commands_execute_page_action.js failed in 31/31 of these jobs".
- **Got:** Only per-job listings, so a shell loop over 30 `fx-tests task` calls was needed.
- **Suggestion:** Add a co-failure view (e.g. `fx-tests test <path> --co-failures`) listing other tests failing in N of this test's M failing jobs.
- **Example from:** [browser/browser/components/extensions/test/browser/browser_ext_webNavigation_onCreatedNavigationTarget_contextmenu.js.files/fx-tests-feedback.md](reports/browser/browser/components/extensions/test/browser/browser_ext_webNavigation_onCreatedNavigationTarget_contextmenu.js.files/fx-tests-feedback.md)

## 9. `test --bugs` misses the job-wide bug sheriffs starred the test's failing job on (63)

`fx-tests test <path> --bugs` only lists bugs whose summary names the test, so job-wide bug 1991833, on which the failing job was starred, never appears. Finding it required already knowing the bug number.

- **Question:** Which bug are this test's failing jobs starred on?
- **Command:** `fx-tests test devtools/shared/transport/tests/xpcshell/test_transport_bulk.js --bugs`
- **Expected:** Bug 1991833, since the only failing job (XfKT04wZR42kSNwV6wuRpQ) is starred on it.
- **Got:** The usual summary, with no bug listed; the bug's summary names no test, so it does not match.
- **Suggestion:** For each failing task id in `--task-ids`, show the bug sheriffs starred it on.
- **Example from:** [devtools/devtools/shared/transport/tests/xpcshell/test_transport_bulk.js.files/fx-tests-feedback.md](reports/devtools/devtools/shared/transport/tests/xpcshell/test_transport_bulk.js.files/fx-tests-feedback.md)

## 10. `fx-tests test` has no web-platform-tests data; `--harness` only accepts xpcshell|mochitest (63)

`fx-tests test` on a WPT path exits 2 with no rates, history or failing tasks, and `--harness` only accepts xpcshell|mochitest. The agent had to combine `intermittent --bug`, Treeherder's jobs API and errorsummary `test_groups` to count runs and find the chunk.

- **Question:** What are this WPT test's per-config failure rates, history and failing tasks?
- **Command:** `fx-tests test testing/web-platform/tests/resize-observer/observer-in-cross-origin-frame.sub.html`
- **Expected:** Rates per config, history and failing tasks for a WPT test.
- **Got:** exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...".
- **Suggestion:** Cover web-platform-tests in `fx-tests test` (rates, `--history`, `--task-ids`, `--coverage`).
- **Example from:** [intermittent/testing/web-platform/tests/resize-observer/observer-in-cross-origin-frame.sub.html.files/fx-tests-feedback.md](reports/intermittent/testing/web-platform/tests/resize-observer/observer-in-cross-origin-frame.sub.html.files/fx-tests-feedback.md)

## 11. `fx-tests test` verdict hides that failures came from job-wide TIMEOUT collapses (60)

`fx-tests test` gave a clean intermittent verdict for a test whose 6 failing jobs each had 476-1563 other TIMEOUTs. Nothing flags job-wide breakage; the agent had to run `fx-tests task` on every task to see it.

- **Question:** Is this test's failure its own, or one of hundreds in a job-wide meltdown?
- **Command:** `fx-tests test devtools/server/tests/xpcshell/test_stepping-15.js`
- **Expected:** Some hint that every one of the 6 failing jobs had 476-1563 other TIMEOUTs.
- **Got:** A clean "intermittent, 1.8% on msix, TIMEOUT 6x" verdict that reads like a test problem.
- **Suggestion:** Per failing run, show the number of other failures in the same job, e.g. "6 of 6 failures were in jobs with >400 failing tests".
- **Example from:** [devtools/devtools/server/tests/xpcshell/test_stepping-15.js.files/fx-tests-feedback.md](reports/devtools/devtools/server/tests/xpcshell/test_stepping-15.js.files/fx-tests-feedback.md)

## 12. `test`/`task` show placeholder instead of harness INFO line for message-less Android FAILs (58)

For an Android xpcshell FAIL with no failure message, `fx-tests test` printed only a placeholder and `fx-tests task` a bare FAIL, though the job's resource-usage profile held the harness INFO line naming the test and explaining the failure.

- **Question:** Why did this Android xpcshell FAIL with no message fail?
- **Command:** `fx-tests task UTfxMnRjTQukP_jTsQCU4A`
- **Expected:** The harness INFO line naming the test: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`.
- **Got:** `Failure details not recorded (likely Android or platform logging issue)` in `test`, and a bare `FAIL — 1 failing execution of 2` in `task`.
- **Suggestion:** When no failure message is recorded, show the harness INFO/WARNING line naming the test from the profile, and group failures by it.
- **Example from:** [devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_takeCensus_02.js.files/fx-tests-feedback.md](reports/devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_takeCensus_02.js.files/fx-tests-feedback.md)

## 13. `failures --message` finds nothing for crash signatures, no pointer to `crashes` (58)

`fx-tests failures --message "child process hang at shutdown"` says "No failure matched" although `fx-tests test` lists that crash under Issues. Crash signatures are not messages there, and nothing points to `crashes --signature`, which does know it but cannot list the tests.

- **Question:** Which other tests fail with this crash signature?
- **Command:** `fx-tests failures --harness xpcshell --message "child process hang at shutdown"`
- **Expected:** The message `test` shows under Issues ("CRASH child process hang at shutdown") to be findable, or a pointer to `crashes --signature`.
- **Got:** "No failure matched. ... Check --path, --message and --component for typos."
- **Suggestion:** When the text is a crash signature, the no-match output should say so and suggest `fx-tests crashes --signature`.
- **Example from:** [devtools/devtools/shared/tests/xpcshell/test_console_filtering.js.files/fx-tests-feedback.md](reports/devtools/devtools/shared/tests/xpcshell/test_console_filtering.js.files/fx-tests-feedback.md)

## 14. `try --limit 0` still truncates per-row task and profile lists (53)

On `fx-tests try <rev> --task-ids --profiles --limit 0`, each test row still stops at 5 task lines and a few profiles followed by "… N more"; tasks are also printed once per failure, so only `--json` gives the full list.

- **Question:** What are all the distinct failing tasks (and profiles) of one test on a try push?
- **Command:** `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids --limit 0`
- **Expected:** `--limit 0` lists every task ID and profile URL behind the test's row.
- **Got:** 5 task lines then `… 37 more tasks`, and 5 profiles then `… 16 more profiles`; each task printed twice, so the 5 shown cover 3 tasks.
- **Suggestion:** Under `--limit 0`, show one uncapped line per distinct task, with its config and profile URLs.
- **Example from:** [devtools/devtools/client/application/test/browser/browser_application_panel_open-links.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/application/test/browser/browser_application_panel_open-links.js.files/fx-tests-feedback.md)

## 15. `task` reports "0 tests" for marionette jobs (`<path> Class.method` markers) (53)

`fx-tests task` on a marionette job said the profile records no tests, though the resource-usage profile has 254 `test` markers. Marionette names tests `<path> Class.method`, which `task` does not parse as a test path, so failing markers "named no test path".

- **Question:** Which tests did this marionette job run, and with which statuses?
- **Command:** `fx-tests task eYq3nlTIQoK7PwfeZlWvbQ --profiles`
- **Expected:** The job's tests with statuses, e.g. `ERROR — .../test_refresh_firefox.py TestFirefoxRefresh.testFxANoSync`.
- **Got:** `0 tests, 0 executions, 0 failing` and "This profile records no tests at all", plus a warning that 4 failing markers "named no test path".
- **Suggestion:** Parse test markers of other harnesses: strip marionette's ` Class.method` suffix, reftest `A == B` form, and similar.
- **Example from:** [intermittent/browser/components/migration/tests/marionette/test_refresh_firefox.py.files/fx-tests-feedback.md](reports/intermittent/browser/components/migration/tests/marionette/test_refresh_firefox.py.files/fx-tests-feedback.md)

## 16. `task` doesn't say which manifest, or when, each execution of a test ran (47)

A test ran from both `browser-dFPI.toml` and `browser.toml` in one job. `fx-tests task` gave one combined count, with no manifest or start time per execution, so the passing run could not be used as a control for the failing one.

- **Question:** Which manifest's run of this test failed in this job, and which passed?
- **Command:** `fx-tests task TM0v1lQzRCeX0ACCSXH-jA`
- **Expected:** For each execution, its manifest and start time next to the outcome.
- **Got:** "FAIL — 1 failing execution of 3", with no manifest or time for any execution.
- **Suggestion:** Show each execution's manifest and start time next to its outcome.
- **Example from:** [mac/dom/serviceworkers/test/browser_pbm_cleanup.js.files/fx-tests-feedback.md](reports/mac/dom/serviceworkers/test/browser_pbm_cleanup.js.files/fx-tests-feedback.md)

## 17. `task --profiles` lists 1154 TIMEOUTs one by one with no job-wide summary (45)

On a job where 1152 of 1552 tests timed out within the same few seconds, `fx-tests task --profiles` printed every entry separately and nothing about the harness traceback (`PermissionError: [WinError 5] Access is denied`) that explained the mass failure.

- **Question:** Why did 1152 tests of this job all TIMEOUT at once?
- **Command:** `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles`
- **Expected:** A line surfacing the harness's `Following exceptions were raised:` traceback, or a note that most TIMEOUTs share one start/end time.
- **Got:** 1154 entries listed one by one, each "TIMEOUT — Test timed out", and nothing about the traceback.
- **Suggestion:** For mass-failure jobs, summarize up front: N TIMEOUTs in one burst at t=…, first failure in time order, plus the harness's final traceback.
- **Example from:** [devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_census_diff_03.js.files/fx-tests-feedback.md](reports/devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_census_diff_03.js.files/fx-tests-feedback.md)

## 18. `test --task-ids` shows no worker per failing task, hiding machine concentration (45)

To check whether a crash was concentrated on some machines, `fx-tests test --task-ids` and `fx-tests task` printed no worker anywhere. The agent had to query Taskcluster and Treeherder per task. The worker turned out to be the whole answer.

- **Question:** Is this failure concentrated on some workers?
- **Command:** `fx-tests test <path> --task-ids`
- **Expected:** The worker (machine name) of each failing task, and a count of jobs per worker as denominator.
- **Got:** No worker anywhere in the output. Scripting found 22 of 78 macOS 10.15 workers produce 96% of this signature.
- **Suggestion:** A `--by-worker` view on `test --task-ids` (fail count / job count per workerId).
- **Example from:** [intermittent/bugs/no-bug.macos1015-child-process-hang-at-shutdown.files/fx-tests-feedback.md](reports/intermittent/bugs/no-bug.macos1015-child-process-hang-at-shutdown.files/fx-tests-feedback.md)

## 19. `test --history --issue N` refused: "--issue needs --task-ids" (42)

`fx-tests test <path> --history --issue 1` exits with an error, so the History table cannot be restricted to one failure mode. With two modes of similar size (754 vs 673), a step change could not be attributed to either.

- **Question:** Did failure mode 1 or mode 2 cause the step change in the per-day history?
- **Command:** `fx-tests test browser/components/aboutlogins/tests/browser/browser_breachAlertLinkTelemetry.js --history --issue 1`
- **Expected:** The History table restricted to runs whose first failure is issue 1.
- **Got:** exit 1, "--issue ... needs --task-ids". Workaround: `--task-ids --issue N --limit 0` plus awk, which counts jobs, not runs, with no pass denominator.
- **Suggestion:** Let `--issue N` filter `--history` (and the per-config table) to that failure mode.
- **Example from:** [intermittent/browser/components/aboutlogins/tests/browser/browser_breachAlertLinkTelemetry.js.files/fx-tests-feedback.md](reports/intermittent/browser/components/aboutlogins/tests/browser/browser_breachAlertLinkTelemetry.js.files/fx-tests-feedback.md)

## 20. Manifest-level LSan shutdown leaks invisible outside `intermittent --bug` (41)

A `LeakSanitizer | leak at …` failure attributed to a manifest (21 orange jobs in 7 days) is absent from `test`, `failures` and `errors`. Only `fx-tests intermittent --bug 2038890` sees it, as sheriff annotations, with no run denominator.

- **Question:** How often does this shutdown leak happen, on which configs, out of how many jobs?
- **Command:** `fx-tests failures --harness mochitest --message FileSystemBackgroundRequestHandler --tests`
- **Expected:** Per-config rates for the LeakSanitizer failure attributed to the manifest.
- **Got:** "No failure matched"; `fx-tests test <manifest>.toml` says "No test path … contains"; `errors --message` finds no markers.
- **Suggestion:** Record manifest-scoped failures (LSan leaks, shutdown leaks) as their own rows keyed by manifest path.
- **Example from:** [intermittent/bugs/2038890.files/fx-tests-feedback.md](reports/intermittent/bugs/2038890.files/fx-tests-feedback.md)

## 21. `intermittent --bug` 7-day default silently misses older occurrences (41)

`fx-tests intermittent --bug 1991833` defaults to 7 days while `fx-tests test` covers 21. It missed the failure `test` reported on 2026-09-10, with no hint that its window was narrower.

- **Question:** Does this bug's occurrence list include the failure `fx-tests test` reported on 2026-09-10?
- **Command:** `fx-tests intermittent --bug 1991833`
- **Expected:** Occurrences covering the failure `fx-tests test` reported (2026-09-10).
- **Got:** 10 occurrences from 2026-09-16 on, no hint the window was narrower; the failing task only showed with `--since 21`.
- **Suggestion:** State the window and how many annotations it (and the trunk filter) left out, or default to the same 21 days as `test`.
- **Example from:** [devtools/devtools/shared/transport/tests/xpcshell/test_bulk_error.js.files/fx-tests-feedback.md](reports/devtools/devtools/shared/transport/tests/xpcshell/test_bulk_error.js.files/fx-tests-feedback.md)

## 22. `test --since` rejects a date, accepts only a day count (37)

`fx-tests test <path> --since <date>` errors out because `--since` only takes a number of days, while `--day` accepts a date. Agents checking the rate after a fix landed had to count days back by hand.

- **Question:** What are this test's runs since the date a fix landed?
- **Command:** `fx-tests test <path> --since 2026-09-18`
- **Expected:** Runs since that date, to check the rate after a fix landed.
- **Got:** `fx-tests: --since expects a non-negative integer, got "2026-09-18"`. Workaround: `--since 3`.
- **Suggestion:** Accept a date in `--since` too, as `--day` does.
- **Example from:** [devtools/devtools/client/inspector/test/browser_inspector_inspect_node_contextmenu.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/inspector/test/browser_inspector_inspect_node_contextmenu.js.files/fx-tests-feedback.md)

## 23. `fx-tests test` "No test path" error reads as wrong path for uncovered harnesses (34)

For a WPT (also marionette, geckoview-junit) path, `fx-tests test` says the test may have been renamed or never run, which reads as a wrong path rather than "this harness is not covered".

- **Question:** Is this WPT test still failing, and since which push?
- **Command:** `fx-tests test testing/web-platform/tests/webcodecs/full-cycle-test.https.any.js`
- **Expected:** Per-config rates and per-day history, or a message saying WPT is not covered.
- **Got:** "No test path in the xpcshell and mochitest 21-day data contains ..., It may have been renamed, added after the window started, or never run in CI."
- **Suggestion:** Say the harness is not covered, and point to commands that work (e.g. `fx-tests intermittent --bug`/`--test`).
- **Example from:** [intermittent/testing/web-platform/tests/webcodecs/full-cycle-test.https.any.js.bug2073708.files/fx-tests-feedback.md](reports/intermittent/testing/web-platform/tests/webcodecs/full-cycle-test.https.any.js.bug2073708.files/fx-tests-feedback.md)

## 24. `fx-tests task` on wpt jobs drops unexpected subtest FAILs, lists expected ERRORs (33)

On a wpt job, `fx-tests task` listed tests whose expected status is ERROR as failures, and none of the tests with TEST-UNEXPECTED-FAIL subtests that actually turned the job orange.

- **Question:** What failed in this wpt job, i.e. why was it orange?
- **Command:** `fx-tests task ErYQo183S-SKqXAu9Wa0lA --profiles`
- **Expected:** `/screen-capture/getdisplaymedia-framerate.https.html` and other screen-capture tests with TEST-UNEXPECTED-FAIL subtests listed.
- **Got:** "FAILED (18)", made of tests whose expected status is ERROR (logging `TEST-ERROR`, not `TEST-UNEXPECTED-ERROR`), and none of the tests with unexpected subtest FAILs.
- **Example from:** [intermittent/testing/web-platform/tests/screen-capture/getdisplaymedia-framerate.https.html.bug1925691.files/fx-tests-feedback.md](reports/intermittent/testing/web-platform/tests/screen-capture/getdisplaymedia-framerate.https.html.bug1925691.files/fx-tests-feedback.md)

## 25. `try --all-jobs --test` doesn't say whether the skipped config was scheduled (33)

`fx-tests try <rev> --all-jobs --test <path>` lists only the configs that ran the test. A missing config (e.g. http3) looks the same whether the push never scheduled it or it ran and skipped the test.

- **Question:** Did the try push schedule the config that the test's exclusion covers?
- **Command:** `fx-tests try 2888bcab0070 --all-jobs --test devtools/client/netmonitor/test/filters/browser_net_filter-flags.js`
- **Expected:** The push's configs that did not run the test, with the reason: "http3 config not scheduled on this push", or "skipped by run-if !http3".
- **Got:** Only the 7 configs that ran it; nothing says whether the `*-http3` config was absent from the push or present but skipping the test.
- **Suggestion:** Under `--test`, list the test's skipped configs (from central) and mark those with no job on the push as "not scheduled on this push".
- **Example from:** [devtools/devtools/client/netmonitor/test/filters/browser_net_filter-flags.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/netmonitor/test/filters/browser_net_filter-flags.js.files/fx-tests-feedback.md)

## 26. `test --task-ids` Issues omit the subtest name that the log line carries (31)

`fx-tests test <path> --task-ids --issue 2` lumped 21 `Test exceeded time limit` jobs into one issue with no subtest, though the log lines name it (14 `test_groupHasActiveTab`, 7 `test_moveTabBetweenGroups`), so one issue hid two hang sites.

- **Question:** Which subtest failed in each job of this issue?
- **Command:** `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_tab_groups.js --task-ids --limit 0 --issue 2`
- **Expected:** The subtest each job failed in, shown next to each task ID or used to split the issue.
- **Got:** The task IDs only. The message has no subtest.
- **Suggestion:** Show the subtest from the log line (`| <subtest> - ...`) next to each task ID, or split the issue by subtest.
- **Example from:** [browser/browser/components/tabbrowser/test/browser/tabs/browser_tab_groups.js.timeout.files/fx-tests-feedback.md](reports/browser/browser/components/tabbrowser/test/browser/tabs/browser_tab_groups.js.timeout.files/fx-tests-feedback.md)

## 27. `test` Issues splits one failure into rows by timestamps, UUIDs or line numbers (31)

`fx-tests test` groups Issues by exact message, so one assertion containing a timestamp became 478 separate `1x` rows (truncated to 10), hiding that every failure is the same mode. UUIDs, GUIDs, `blob:` URIs and line drift cause the same split.

- **Question:** How many of this test's failures share one failure mode?
- **Command:** `fx-tests test browser/components/search/test/browser/browser_search_lastused_pref.js --harness mochitest`
- **Expected:** One Issues row: `478x FAIL The urlbar search was recorded - N > N`.
- **Got:** 478 rows of `1x`, one per timestamp (`... - 1788154863 > 1788154863`, `... - 1788157763 > 1788157763`, ...), truncated to 10 with "… 468 more".
- **Suggestion:** Normalize long numbers, hex addresses, pids, UUIDs and long `data:` URIs before grouping Issues.
- **Example from:** [mac/browser/components/search/test/browser/browser_search_lastused_pref.js.files/fx-tests-feedback.md](reports/mac/browser/components/search/test/browser/browser_search_lastused_pref.js.files/fx-tests-feedback.md)

## 28. `test --task-ids` groups all TIMEOUTs as one issue, hiding preceding errors (31)

`fx-tests test <path> --task-ids` put all 19 timeouts under one `TIMEOUT Test exceeded time limit` issue. The log lines just before each timeout showed three distinct mechanisms, which the agent only found by grepping every task's log.

- **Question:** What distinguishes the 19 timeouts of this test?
- **Command:** `fx-tests test <path> --task-ids --limit 0`
- **Expected:** Timeouts split by the error logged before them (`savedFieldNames is null`, `Services.ww.activeWindow is null`, neither).
- **Got:** a single issue, `TIMEOUT Test exceeded time limit`, for all 19.
- **Suggestion:** Show the JS errors or first ERROR lines logged during the failing test, grouped per task.
- **Example from:** [intermittent/browser/extensions/formautofill/test/mochitest/creditCard/test_basic_creditcard_autocomplete_form.html.files/fx-tests-feedback.md](reports/intermittent/browser/extensions/formautofill/test/mochitest/creditCard/test_basic_creditcard_autocomplete_form.html.files/fx-tests-feedback.md)

## 29. `intermittent --bug` failure messages skip occurrences with no TEST-UNEXPECTED-FAIL line (31)

On a raptor perf bug, `fx-tests intermittent --bug` showed failure messages for only 2 of 32 annotated jobs, the two with a `TEST-UNEXPECTED-FAIL` line. The bug's real `MissingResultsError` lines were left out, with no count of the skipped jobs.

- **Question:** What was the failure message of each annotated job, so mis-starred jobs stand out?
- **Command:** `fx-tests intermittent --bug 1682027 --limit 0`
- **Expected:** The failure message of each of the 32 annotated jobs.
- **Got:** "Failure messages, per annotated job" listed only 2; the 30 raptor `results.MissingResultsError: ...` lines and the talos `Automation Error: mozharness timed out` were missing.
- **Suggestion:** For occurrences with no TEST-UNEXPECTED line, fall back to the job's error lines (e.g. Treeherder `text_log_errors`), and count occurrences that have no line.
- **Example from:** [intermittent/bugs/1682027.files/fx-tests-feedback.md](reports/intermittent/bugs/1682027.files/fx-tests-feedback.md)

## 30. `task --profiles` omits the `profile_0_<pid>.json` hang profile of timed-out tests (31)

For a test ending in "application timed out after 370.0 seconds with no output" on macOS, the harness uploads `public/test_info/profile_0_<pid>.json`, the only profile showing the deadlocked main thread. `fx-tests task --profiles` doesn't list it and says there is none.

- **Question:** Where is the profile of the hung browser for this "application timed out" failure?
- **Command:** `fx-tests task NMbnCN1zQtmQXT0c9L3UoA.0 --profiles`
- **Expected:** The harness-captured `profile_0_<pid>.json` listed under the hung test.
- **Got:** "No failing test named a per-test profile in this job", which reads as "there is nothing to load".
- **Suggestion:** List `profile_0_<pid>.json` artifacts under the test whose hang matches the pid in the log's "Sending SIGUSR1 to pid" line.
- **Example from:** [mac/browser/components/tests/browser/browser_browserGlue_client_association_ping.js.files/fx-tests-feedback.md](reports/mac/browser/components/tests/browser/browser_browserGlue_client_association_ping.js.files/fx-tests-feedback.md)

## 31. `fx-tests task` prints only a short hg hash, no git hash (30)

`fx-tests task` shows the revision as a short hg hash only. hg.mozilla.org raw-file now returns HTTP 406, so the agent had to map hg to git through lando before running `git show` locally.

- **Question:** At which revision do I read the code of this failing task?
- **Command:** `fx-tests task UU8IoiQLTQCZFBLbgh9g3g.0`
- **Expected:** A revision usable to read the source locally.
- **Got:** `autoland 1ff3ffd34ab8` (an hg hash); `curl .../raw-file/1ff3ffd34ab8/<path>` returns HTTP 406 with an empty body.
- **Suggestion:** Print the git hash next to the hg one.
- **Example from:** [browser/browser/components/places/tests/unit/test_browserGlue_corrupt_nobackup.js.files/fx-tests-feedback.md](reports/browser/browser/components/places/tests/unit/test_browserGlue_corrupt_nobackup.js.files/fx-tests-feedback.md)

## 32. `fx-tests try` refuses `--config`, even together with `--test` (29)

`fx-tests try <rev> --all-jobs --config browser-chrome --test <path>` is rejected, so the report cannot be narrowed to the relevant jobs. The agent had to read all 236 jobs of the push, which took several minutes.

- **Question:** Can the try report for one test be narrowed to one job type to make `--all-jobs` fast?
- **Command:** `fx-tests try <rev> --all-jobs --config browser-chrome --test <path>`
- **Expected:** `--config` narrowing the jobs read, harmless since `--test` already restricts the report to one test.
- **Got:** exit 1, "--config cannot be applied to try: ... filtering the job set would change what each section means".
- **Suggestion:** Accept `--config` on `try`, at least with `--test`.
- **Example from:** [mac/browser/components/privatebrowsing/test/browser/browser_privatebrowsing_about_default_pin_promo.js.files/fx-tests-feedback.md](reports/mac/browser/components/privatebrowsing/test/browser/browser_privatebrowsing_about_default_pin_promo.js.files/fx-tests-feedback.md)

## 33. `fx-tests task` omits the harness traceback explaining a job-wide TIMEOUT collapse (27)

`fx-tests task` on a job where 1152 of 1552 tests timed out together lists each TIMEOUT one by one, but not the harness's `PermissionError: [WinError 5] Access is denied` traceback (from `CreateProcess`) that explains them all.

- **Question:** Why did 1152 tests of this job all TIMEOUT at once?
- **Command:** `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles`
- **Expected:** The harness's `Following exceptions were raised:` traceback, or a note that most TIMEOUTs share one start/end time.
- **Got:** 1154 entries listed one by one, each "TIMEOUT — Test timed out", and nothing about the traceback.
- **Suggestion:** Show job-level harness ERROR/traceback lines and flag mass failures (e.g. "N tests TIMEOUT, starting within X s").
- **Example from:** [devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_census_diff_03.js.files/fx-tests-feedback.md](reports/devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_census_diff_03.js.files/fx-tests-feedback.md)

## 34. `task --profiles` silently omits the profile line for a failing test (26)

`fx-tests task FN28SmhaRQOc78Qf-Okjrw --profiles` showed profile lines for other rows but none for `test_TelemetryFeed.js`, with no note that one was missing or why (the test stops the profiler itself).

- **Question:** Which profile covers this test's failure?
- **Command:** `fx-tests task FN28SmhaRQOc78Qf-Okjrw --profiles`
- **Expected:** A profile line for `test_TelemetryFeed.js`, or an explicit "no per-test profile was uploaded for this failure".
- **Got:** The row for this test has no profile line, while the other rows do; nothing says one is missing.
- **Suggestion:** Print "no profile uploaded" on the row, with the reason when known (profiler stopped by test, crash, shutdown leak).
- **Example from:** [browser/browser/extensions/newtab/test/xpcshell/test_TelemetryFeed.js.files/fx-tests-feedback.md](reports/browser/browser/extensions/newtab/test/xpcshell/test_TelemetryFeed.js.files/fx-tests-feedback.md)

## 35. `test` ignores a renamed test's history under its old path (26)

`fx-tests test` on a test moved mid-window (bug 2069131) reported it as passing, with zero runs before the rename and no mention of the old path; all 5 failures were only visible by querying the old path.

- **Question:** What were this test's failures in the window, including those before its directory rename?
- **Command:** `fx-tests test browser/base/content/test/browser-tabPrompts/browser_confirmFolderUpload.js`
- **Expected:** Failures including those under the old path `browser/base/content/test/tabPrompts/...`, or a note that an older path has history.
- **Got:** "Verdict: passing. 1,130 of 1,130 runs passed."; `--history` shows zero runs before 2026-09-16, no mention of the old path.
- **Suggestion:** Show an "also known as" line, or fold in the old path's counts, when a rename is known.
- **Example from:** [mac/browser/base/content/test/browser-tabPrompts/browser_confirmFolderUpload.js.files/fx-tests-feedback.md](reports/mac/browser/base/content/test/browser-tabPrompts/browser_confirmFolderUpload.js.files/fx-tests-feedback.md)

## 36. `test` Issues count only each run's first failure; later messages invisible (25)

`fx-tests test --task-ids` groups Issues by first failure per run, so a failure mode that appears as a later message is undercounted. Finding every run containing it required `fx-tests task <id> --messages` one job at a time.

- **Question:** In how many runs did this failure mode occur at all, not only as the first failure?
- **Command:** `fx-tests test gfx/layers/apz/test/mochitest/test_group_keyboard.html --task-ids --limit 0`
- **Expected:** A count of runs per failure mode, including runs where the mode was a later message.
- **Got:** "Issues (first failure per run)": the backward-jump mode shows up in 3 rows, but it is in 7 of the 15 failing runs.
- **Suggestion:** Add a column or `--issue <n> --any-position` counting runs containing a matching message anywhere, with their task ids.
- **Example from:** [mac/gfx/layers/apz/test/mochitest/test_group_keyboard.html.backward-jump.files/fx-tests-feedback.md](reports/mac/gfx/layers/apz/test/mochitest/test_group_keyboard.html.backward-jump.files/fx-tests-feedback.md)

## 37. `fx-tests crashes --signature` text shows test count, not which tests (25)

`fx-tests crashes --signature` printed a single row with crash and test counts but no test names. The names exist only in `--json` `rows[].tests[]`, and `crashes` has no `--tests` flag like `failures`.

- **Question:** Which tests are behind this crash signature?
- **Command:** `fx-tests crashes --signature panic_hook --since 7`
- **Expected:** The tests behind the signature.
- **Got:** One row, `279 crashes / 102 tests`, no test names; `--tests` exists on `failures` but not on `crashes`.
- **Suggestion:** List the top tests under the row in text output, like `failures --tests` does.
- **Example from:** [browser/browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_defaultPrefs.js.files/fx-tests-feedback.md](reports/browser/browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_defaultPrefs.js.files/fx-tests-feedback.md)

## 38. Default `try` output truncates sections, hiding a test's failure from grep (25)

`fx-tests try <rev> --profiles --task-ids` truncates each section to 10 rows ("… N more"), and has no per-test filter keeping failure rows, so grepping for a failing test found nothing and it looked like it had not failed.

- **Question:** Did test X fail on this try push, and where are its profiles?
- **Command:** `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids`
- **Expected:** A way to scope the push view to one test path, e.g. `--test accessible/tests/mochitest/events/test_valuechange.html`.
- **Got:** 744 lines, perma-fails truncated to 10 of 51 (`… 41 more (--limit 0 for all)`); the test was not in the shown part.
- **Suggestion:** Let `--test <path>` narrow the default failure view, printing that test's messages, task IDs and profile URLs.
- **Example from:** [mac/accessible/tests/mochitest/events/test_valuechange.html.files/fx-tests-feedback.md](reports/mac/accessible/tests/mochitest/events/test_valuechange.html.files/fx-tests-feedback.md)

## 39. `test` Issues say "Failure details not recorded" when `task` shows the message (24)

`fx-tests test dom/base/test/test_bug331959.html` lists all 36 macOS failures as "Failure details not recorded (likely Android...)", while `fx-tests task` shows their message. The aggregate dropped a message that exists.

- **Question:** What is the failure message of this test's failures?
- **Command:** `fx-tests test dom/base/test/test_bug331959.html`
- **Expected:** Issues showing `[SimpleTest.finish()] No checks actually run. ...`, as `fx-tests task RvTP9NRFTcyoxaOUB4101w.0` shows.
- **Got:** `36x FAIL Failure details not recorded (likely Android or platform logging issue)` for all 36 failures, on macOS.
- **Example from:** [intermittent/dom/base/test/test_bug331959.html.files/fx-tests-feedback.md](reports/intermittent/dom/base/test/test_bug331959.html.files/fx-tests-feedback.md)

## 40. `fx-tests test` has no geckoview-junit data and doesn't say so (24)

`fx-tests test` on a geckoview-junit test returns the generic "No test path ... contains" error, with no rates, history or task IDs, and no hint that junit is simply not indexed, so it reads like a typo.

- **Question:** How often does this junit test fail, and on which configs, relative to its runs?
- **Command:** `fx-tests test DynamicToolbarTest`
- **Expected:** Rates per config, history and task IDs for the test (ids are `org.mozilla.geckoview.test.DynamicToolbarTest#method`).
- **Got:** `No test path in the xpcshell and mochitest 21-day data contains ...`, with no hint that junit is simply not indexed.
- **Suggestion:** Say geckoview-junit is not covered and point at `intermittent --bug`; or index junit tests from the resource-usage profile's `test` markers.
- **Example from:** [intermittent/mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/DynamicToolbarTest.kt.bug2033703.files/fx-tests-feedback.md](reports/intermittent/mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/DynamicToolbarTest.kt.bug2033703.files/fx-tests-feedback.md)

## 41. `intermittent --bug` ignores marionette TEST-UNEXPECTED-ERROR lines in failure messages (24)

`fx-tests intermittent --bug 2072442` listed failure messages for only 4 of 25 annotated jobs. The other 21 failed with `TEST-UNEXPECTED-ERROR` on the bug's marionette test, which appears nowhere, so those jobs read as having no test failure.

- **Question:** Which test failures are in the jobs sheriffs starred on this bug?
- **Command:** `fx-tests intermittent --bug 2072442`
- **Expected:** The failing tests of all 25 annotated jobs.
- **Got:** Only 3x test_cache_encryption_flip_purge.py and 1x test_backup_selectable_to_legacy.py; `TEST-UNEXPECTED-ERROR | .../test_new_tab_on_restore.py` (21 jobs) appears nowhere.
- **Suggestion:** Parse TEST-UNEXPECTED-ERROR (and TIMEOUT/CRASH) lines too, and count jobs with no parsed message.
- **Example from:** [intermittent/bugs/2072442.files/fx-tests-feedback.md](reports/intermittent/bugs/2072442.files/fx-tests-feedback.md)

## 42. `intermittent --bug` shows no pass/fail history for the annotated job (24)

For a bug on a lint job, `fx-tests intermittent --bug` listed only the last 7 days of annotations. It couldn't say when the job started failing or whether any run passed since, and `fx-tests test` needs a test path.

- **Question:** When did this lint job start failing, and has any run passed since?
- **Command:** `fx-tests intermittent --bug 2068807`
- **Expected:** The job's pass/fail runs per day, or at least all annotations since the bug was filed.
- **Got:** The 6 annotations from the last 7 days only. Treeherder showed 22/22 testfailed since the 09-03 cron.
- **Suggestion:** When a bug's occurrences come from one job name, show that job's run history (pass/fail) over the window, and flag occurrences from other job names.
- **Example from:** [intermittent/bugs/2068807.files/fx-tests-feedback.md](reports/intermittent/bugs/2068807.files/fx-tests-feedback.md)

## 43. `task --profiles` says the resource profile expired when it was never uploaded (23)

On a one-day-old jit-test task, `fx-tests task --profiles` said the missing `profile_resource-usage.json` had expired and was gone for good. The jittest harness never uploads one, so blaming expiry was misleading.

- **Question:** What artifacts does this task have, given it has no profile?
- **Command:** `fx-tests task Xb7w71mJQoGVqsjF2kvzHQ --profiles`
- **Expected:** The job's failures from its log, or at least the list of artifacts that do exist.
- **Got:** "the artifact is not there. Taskcluster expires task artifacts after about a month, so this is permanent", for a task one day old.
- **Suggestion:** Only blame expiry for tasks older than about a month; otherwise say the job type never uploads one and list existing artifacts.
- **Example from:** [intermittent/bugs/2041498.files/fx-tests-feedback.md](reports/intermittent/bugs/2041498.files/fx-tests-feedback.md)

## 44. `test --bugs` finds no bugs for a moved test filed under its old path (22)

The test was renamed in bug 2069131. `fx-tests test --bugs` on the new path printed no bugs section, and `--json` had `"annotatedBugs": []`. The same command on the old path returned both bugs.

- **Question:** Which bugs are filed for this test?
- **Command:** `fx-tests test browser/base/content/test/browser-performance/browser_startup_content_mainthreadio.js --bugs`
- **Expected:** Bugs 2071154 and 2071550, or a line saying none were found under this path and bugs may name its previous path.
- **Got:** The normal output with no bugs section; `--json` has `"annotatedBugs": []`. The old path (`.../test/performance/...`) returns both bugs.
- **Suggestion:** Also match bugs naming the same file name under another directory (the test's previous path).
- **Example from:** [intermittent/browser/base/content/test/browser-performance/browser_startup_content_mainthreadio.js.bug2071154.files/fx-tests-feedback.md](reports/intermittent/browser/base/content/test/browser-performance/browser_startup_content_mainthreadio.js.bug2071154.files/fx-tests-feedback.md)

## 45. `test --history` says "stopped failing" because jobs killed at maxRunTime are dropped (22)

`fx-tests test --history` showed 0 failures since 2026-09-11, while sheriffs starred 16 jobs in the same days. All were killed at `maxRunTime`, so their per-test data never reached the aggregate, with no note that jobs were unreadable.

- **Question:** Is this test still failing on macosx1500-aarch64-vms in recent days?
- **Command:** `fx-tests test dom/base/test/test_bug331959.html --history`
- **Expected:** The 2026-09-16 to 2026-09-20 failures counted, or a line saying some jobs could not be read.
- **Got:** 0 failures every day since 2026-09-11, `recent 0.0%` on every config; `fx-tests intermittent --bug 2031724` lists 16 annotated jobs.
- **Suggestion:** Say per config how many jobs were killed at max-run-time and not read, or count failures from their uploaded per-test profiles.
- **Example from:** [mac/dom/base/test/test_bug331959.html.files/fx-tests-feedback.md](reports/mac/dom/base/test/test_bug331959.html.files/fx-tests-feedback.md)

## 46. `fx-tests task --profiles` refuses jobs killed at max-run-time despite uploaded profiles (21)

For a job killed at maximum duration, `fx-tests task --profiles` refuses to read anything and lists no profile URLs, although the 11 per-test `profile_test_*.json` artifacts were uploaded.

- **Question:** Where are the per-test failure profiles of this job killed at max-run-time?
- **Command:** `fx-tests task QVroiDtfQn6CDTendUZN9g --profiles`
- **Expected:** The per-test failure profiles (`public/test_info/profile_test_*.json`), even if the resource-usage profile is a partial stream.
- **Got:** "was killed for exceeding its maximum duration ... there are no per-test results to read", and no profile URLs.
- **Suggestion:** For killed jobs, list the `profile_*.json` artifacts and fall back to the errorsummary log.
- **Example from:** [intermittent/dom/base/test/chrome/test_bug418986-1.xhtml.bug2073837.files/fx-tests-feedback.md](reports/intermittent/dom/base/test/chrome/test_bug418986-1.xhtml.bug2073837.files/fx-tests-feedback.md)

## 47. `failures --message` can't find the "Failure details not recorded" label `test` prints (20)

`fx-tests test` lists Android failures as "Failure details not recorded", but `fx-tests failures --message` with that text finds nothing because the label is synthetic, so the tests sharing this failure can't be listed.

- **Question:** Which tests and jobs does this harness failure ("Could not kill left-over process") hit?
- **Command:** `fx-tests failures --harness xpcshell --message "Failure details not recorded"`
- **Expected:** The tests (and ideally jobs) whose failure has this message, to size a harness-level failure mode.
- **Got:** `No failure matched` (the "Failure details not recorded" label is synthetic, not searchable).
- **Suggestion:** Make `failures --message` match the labels `test` prints, or use the harness's `Failed to start process: ...` INFO line as the message.
- **Example from:** [devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_getCensusIndividuals_01.js.files/fx-tests-feedback.md](reports/devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_getCensusIndividuals_01.js.files/fx-tests-feedback.md)

## 48. `test` Issues keyed on SimpleTest "flaky timeouts are expected" info line (20)

`fx-tests test` grouped all 291 failures under the `requestFlakyTimeout` line every run prints, not the real `TEST-UNEXPECTED-FAIL` that follows it. `--json` `.messages` has the same single entry, so failure modes can't be split.

- **Question:** Which failure modes does this test have, and how many of each?
- **Command:** `fx-tests test dom/media/webrtc/tests/mochitests/test_peerConnection_stats.html`
- **Expected:** The 291 FAILs split by actual message, mostly `remote-outbound-rtp.remoteTimestamp is on the same timeline as ...`.
- **Got:** One row: `291x FAIL The author of the test has indicated that flaky timeouts are expected.  Reason: WebRTC inherently depends on timeouts`.
- **Suggestion:** Skip known-fail/info lines when picking a run's first failure message.
- **Example from:** [intermittent/dom/media/webrtc/tests/mochitests/test_peerConnection_stats.html.files/fx-tests-feedback.md](reports/intermittent/dom/media/webrtc/tests/mochitests/test_peerConnection_stats.html.files/fx-tests-feedback.md)

## 49. `test --task-ids --issue` lists the same jobs twice, restarting date headers (20)

`fx-tests test --task-ids --limit 0 --issue 4` printed the task list, then restarted the date headers with no section header and listed the macOS jobs again, so counting lines doubled the job count (an earlier report said "up to 28 macosx1500 jobs").

- **Question:** Which jobs failed with this issue, each listed once?
- **Command:** `COLUMNS=300 fx-tests test toolkit/components/downloads/test/unit/test_DownloadCore.js --task-ids --limit 0 --issue 4`
- **Expected:** Each of the 177 jobs listed once, under its date.
- **Got:** After 2026-09-18, date headers restart at 2026-09-01 with no section header; the 14 macOS jobs are listed again. Counting `macosx` lines gives 28 instead of 14.
- **Suggestion:** List each job once, or give each extra group a header saying what it is.
- **Example from:** [mac/toolkit/components/downloads/test/unit/test_DownloadCore.js.xattr-wherefroms.files/fx-tests-feedback.md](reports/mac/toolkit/components/downloads/test/unit/test_DownloadCore.js.xattr-wherefroms.files/fx-tests-feedback.md)

## 50. `fx-tests test` has no data for marionette tests (20)

`fx-tests test` on a marionette test fails with "No test path in the xpcshell and mochitest 21-day data", giving no rates or run counts, and doesn't point to `fx-tests intermittent`, which does work for it.

- **Question:** How often does this marionette test fail, per config?
- **Command:** `fx-tests test netwerk/test/marionette/test_beacon_on_pagehide_shutdown.py`
- **Expected:** Rates per config, or a pointer to the command that covers marionette tests.
- **Got:** `No test path in the xpcshell and mochitest 21-day data contains ...`
- **Suggestion:** When the path looks like a marionette test (`.py` under `*/marionette/`), suggest `fx-tests intermittent --test <path>` / `--bug <N>`.
- **Example from:** [intermittent/netwerk/test/marionette/test_beacon_on_pagehide_shutdown.py.files/fx-tests-feedback.md](reports/intermittent/netwerk/test/marionette/test_beacon_on_pagehide_shutdown.py.files/fx-tests-feedback.md)

## 51. No way to group a test's many crash dumps by process and frame (20)

To see how a test's 65 `child process hang at shutdown` dumps split by process and hang location, the agent had to loop `fx-tests crash <task> <dump>` over every dump; the fixed signature says nothing about where it hung.

- **Question:** Which stacks are behind this test's crash signature: how do its dumps split by process type and top frame?
- **Command:** `fx-tests crash <task> <dump>`
- **Expected:** For one test, the dumps behind `child process hang at shutdown` split by process type and top meaningful frame.
- **Got:** One dump at a time; the signature is a fixed override, so it says nothing about the hang location.
- **Suggestion:** Group a test's dumps by crashing process name and first non-system frame, e.g. "50x GPU Helper @ nsAppShell::Init, 15x GPU Helper @ RenderThread::ShutDown".
- **Example from:** [browser/browser/components/asrouter/tests/xpcshell/test_ASRouterTelemetry.js.files/fx-tests-feedback.md](reports/browser/browser/components/asrouter/tests/xpcshell/test_ASRouterTelemetry.js.files/fx-tests-feedback.md)

## 52. `test --task-ids` misses failing jobs that `task` shows (18)

For a one-day regression, `fx-tests test <path> --task-ids` counted 8 failures in 6 jobs. `fx-tests task` on other Mac jobs of the same pushes found the test failing in 11 more jobs: 30 failing executions in 17 jobs.

- **Question:** In which jobs did this test fail during a one-day regression?
- **Command:** `fx-tests test toolkit/content/tests/chrome/test_menulist_scrollbar.xhtml --task-ids --limit 0`
- **Expected:** Every Mac 15.30 job of autoland pushes 277603..277622 where the test failed.
- **Got:** 8 failures in 6 jobs. 11 more `opt-mochitest-chrome-no-nv*` jobs show it failing 2 of 2 in `task --passed --json`.
- **Example from:** [mac/toolkit/content/tests/chrome/test_menulist_scrollbar.xhtml.files/fx-tests-feedback.md](reports/mac/toolkit/content/tests/chrome/test_menulist_scrollbar.xhtml.files/fx-tests-feedback.md)

## 53. `intermittent --bug` occurrence rows don't show each occurrence's failure message (18)

`fx-tests intermittent --bug` prints aggregated failure messages and a separate Occurrences table with task IDs, with no join between them. Finding the task behind one rare message needs `--json` (`occurrenceRows[].lines`) and a script.

- **Question:** Which failure message did each annotated occurrence have, to find the task with the rare message?
- **Command:** `fx-tests intermittent --bug 2065417 --since 30 --tree all --full-messages --limit 0`
- **Expected:** The Occurrences table carries each occurrence's failure line, or a `--message <text>` filter exists.
- **Got:** Messages aggregated with counts, and a separate occurrences table with task ids but no message; per-occurrence `lines` only in `--json`.
- **Suggestion:** Add a message column to occurrence rows, or a `--message <substr>` filter on the occurrences.
- **Example from:** [intermittent/testing/web-platform/tests/fetch/api/credentials/cookies.any.js.bug2065417.files/fx-tests-feedback.md](reports/intermittent/testing/web-platform/tests/fetch/api/credentials/cookies.any.js.bug2065417.files/fx-tests-feedback.md)

## 54. `test --task-ids` shows no minidump IDs for Windows hang dumps (CRASH+TIMEOUT) (18)

`fx-tests test <path> --task-ids` is documented to print "the minidump IDs of any crashes", but for jobs where the harness killed a hung browser (CRASH+TIMEOUT) it printed task IDs only, and `task` printed none either.

- **Question:** What is the hang dump of the parent process for this timeout?
- **Command:** `fx-tests test browser/components/tests/browser/browser_browserGlue_client_association_ping.js --task-ids --limit 0`
- **Expected:** Minidump IDs per task, e.g. parent dump 9739bac6-8f2a-43c2-b2c6-0beedc1f0f6b for WgE0R749TTWIoeD7iLQtmg.
- **Got:** task IDs only, no minidump IDs; `fx-tests task` also printed none.
- **Suggestion:** List parent (and child) dump IDs for timeout-kills in `task` and `--task-ids`, same as for crashes.
- **Example from:** [intermittent/browser/components/tests/browser/browser_browserGlue_client_association_ping.js.files/fx-tests-feedback.md](reports/intermittent/browser/components/tests/browser/browser_browserGlue_client_association_ping.js.files/fx-tests-feedback.md)

## 55. `test --task-ids` prints one unlabelled task list per failure mode (17)

`fx-tests test <path> --task-ids --limit 0` printed two date-ordered lists back to back under one header. Nothing said which list was which failure mode, so it took one rerun per `--issue`.

- **Question:** Which failing tasks belong to which failure mode?
- **Command:** `fx-tests test dom/security/test/https-only/test_resource_upgrade.html --task-ids --limit 0`
- **Expected:** Task IDs grouped under the issue (failure message) they showed, or an issue column.
- **Got:** Two date-ordered lists under one `Task IDs (165 jobs)` header, nothing saying the first is the `websocket-unexpected-error` FAIL and the second the TIMEOUT.
- **Suggestion:** Put a header per issue above each group, or print the issue number next to each task ID.
- **Example from:** [intermittent/dom/security/test/https-only/test_resource_upgrade.html.files/fx-tests-feedback.md](reports/intermittent/dom/security/test/https-only/test_resource_upgrade.html.files/fx-tests-feedback.md)

## 56. `test --task-ids` chunk labels disagree with `fx-tests task` job names (16)

`fx-tests test <path> --task-ids --limit 0` labeled tasks with the wrong chunk (`-chrome-1`) or no chunk at all, while `fx-tests task` names all 28 tasks `...-chrome-7`. The JSON `chunk` field has the same problem.

- **Question:** Which job (chunk) did each failing task run in?
- **Command:** `fx-tests test browser/components/sidebar/tests/browser/browser_history_keyboard_navigation.js --task-ids --limit 0`
- **Expected:** The same job name as `fx-tests task` shows for each task.
- **Got:** SqjE3rcdTyyg9fn2n0K1hg as `test-macosx1500-aarch64/opt-mochitest-browser-chrome-1`, BMFOuIyvTPqzBQnP3eMo7g as `...-chrome` (no chunk); `task` names both `...-chrome-7`.
- **Suggestion:** Take the chunk from the Taskcluster job name, as `fx-tests task` does.
- **Example from:** [mac/browser/components/sidebar/tests/browser/browser_history_keyboard_navigation.js.files/fx-tests-feedback.md](reports/mac/browser/components/sidebar/tests/browser/browser_history_keyboard_navigation.js.files/fx-tests-feedback.md)

## 57. `task` on gtest job lists GTEST_SKIP'd tests as failing markers (16)

On a gtest job, `fx-tests task --profiles` reported "0 tests". Its failing-marker warning led with GTEST_SKIP'd tests (logged `TEST-FAIL`, expected), so the real failures were buried in "and 10 more".

- **Question:** What failed in this gtest job?
- **Command:** `fx-tests task Vi42DZCaTYW_pc0GpCIvzA --profiles`
- **Expected:** The failing gtests: DesktopLauncherDownloaderReal.DownloadBetaStub and DownloadDevStub.
- **Got:** "0 tests, 0 executions, 0 failing". "15 failing markers ... FontFallbackTest.ListFontsUsedForString_FallbackOrder, ... WebGLTextureUploadInit..." listed first; these are GTEST_SKIP'd.
- **Suggestion:** Leave expected `TEST-FAIL` (skips/assumption failures) out of the failing markers; count only TEST-UNEXPECTED results.
- **Example from:** [intermittent/bugs/2035284.files/fx-tests-feedback.md](reports/intermittent/bugs/2035284.files/fx-tests-feedback.md)

## 58. `fx-tests errors --message` can't find harness INFO lines like "Could not kill left-over process" (16)

`fx-tests errors` holds only C++/JS warnings and errors, not harness INFO lines, and `fx-tests failures --message` can't match the synthetic "Failure details not recorded" label. So a harness-level Android xpcshell failure mode could not be counted tree-wide.

- **Question:** Which tests and jobs does the harness message "Could not kill left-over process" hit?
- **Command:** `fx-tests errors --harness xpcshell --message "Could not kill left-over process"`
- **Expected:** The tests (and ideally jobs) whose failure is this harness message.
- **Got:** `No markers matched`; `failures --message "Failure details not recorded"` gave `No failure matched`.
- **Suggestion:** Index harness INFO lines in `errors`, or surface the last `remotexpcshelltests.py | Failed to start process:` line for the test.
- **Example from:** [devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_getCensusIndividuals_01.js.files/fx-tests-feedback.md](reports/devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_getCensusIndividuals_01.js.files/fx-tests-feedback.md)

## 59. Crash signatures shown without MOZ_CRASH reason in `task`, `test` and `crash` (15)

The signature `@ mozilla::net::CheckForBrokenChromeURL` is shared by every missing-URL crash tree-wide, but `task --messages`, `test` and `crash` show only the signature and stack, never the `MOZ_CRASH(...)` reason that tells occurrences apart.

- **Question:** Which URL is missing in each occurrence of this MOZ_CRASH signature?
- **Command:** `fx-tests task <task> --messages`
- **Expected:** The crash reason (`MOZ_CRASH(Missing chrome or resource URLs: chrome://global/locale/css.properties)`) next to the signature.
- **Got:** Only `@ mozilla::net::CheckForBrokenChromeURL`; `fx-tests crash --raw` has no crash reason either.
- **Suggestion:** Add a `reason` column in `test --task-ids` (from the replayed `Hit MOZ_CRASH(...)` line) and group Issues by reason.
- **Example from:** [mac/toolkit/mozapps/extensions/test/xpcshell/test_AddonRepository_cache_locale.js.files/fx-tests-feedback.md](reports/mac/toolkit/mozapps/extensions/test/xpcshell/test_AddonRepository_cache_locale.js.files/fx-tests-feedback.md)

## 60. `task <id>` without run suffix reads run .0 and calls the artifact permanently gone (15)

`fx-tests try` listed `e6fPXv0dTUWlpLrNpuNcMw.1`, but `fx-tests task e6fPXv0dTUWlpLrNpuNcMw --profiles` read run .0, which never uploaded a profile, and said the artifact expired permanently for a one-day-old push.

- **Question:** What does the profile of this listed failing try task show?
- **Command:** `fx-tests task e6fPXv0dTUWlpLrNpuNcMw --profiles`
- **Expected:** The run `fx-tests try` just listed (`.1`), or a note that run .0 has no profile but run .1 does.
- **Got:** "task e6fPXv0dTUWlpLrNpuNcMw.0 has no profile_resource-usage.json ... Taskcluster expires task artifacts after about a month, so this is permanent".
- **Suggestion:** Default to the failed or latest run, or say which other runs exist before the expiry wording.
- **Example from:** [devtools/devtools/client/styleeditor/test/browser_styleeditor_sourcemap_watching.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/styleeditor/test/browser_styleeditor_sourcemap_watching.js.files/fx-tests-feedback.md)

## 61. `test` counts harness launch failures as test TIMEOUTs (14)

`fx-tests test` reported `6x TIMEOUT` for a test that never started: CreateProcess raised PermissionError and the harness kill timer fired 45 s later. Counting these as timeouts sends people to the test.

- **Question:** Is this test's failure its own, or a job-wide breakage?
- **Command:** `fx-tests test devtools/server/tests/xpcshell/test_framebindings-03.js`
- **Expected:** Some hint the jobs broke (each failing job had 477-1564 failing tests), not a test timeout.
- **Got:** "Verdict: intermittent", "6x TIMEOUT Test exceeded time limit". Nothing suggests the job itself broke.
- **Suggestion:** Flag launch failures at job level; the "Access is denied" traceback is in each job's log.
- **Example from:** [devtools/devtools/server/tests/xpcshell/test_framebindings-03.js.files/fx-tests-feedback.md](reports/devtools/devtools/server/tests/xpcshell/test_framebindings-03.js.files/fx-tests-feedback.md)

## 62. `task` says "harness died" for marionette jobs that ran hundreds of tests (14)

`fx-tests task --profiles` on a marionette job reports 0 tests and suggests the harness died, though the job ran 268 tests. Marionette's resource-usage profile has no test markers, so the verdict is misleading.

- **Question:** What were the test outcomes of this marionette job?
- **Command:** `fx-tests task L01F0C9UReuP3nPBJtiANg --profiles`
- **Expected:** The job's test outcomes (268 passed, 1 failed per the log).
- **Got:** "0 tests, 0 executions ... This profile records no tests at all. Either the job is not a test job, or the harness died before it ran one".
- **Suggestion:** Say this harness records no test markers, and point to `public/test_info/marionette_errorsummary.log`.
- **Example from:** [intermittent/bugs/2072442.files/fx-tests-feedback.md](reports/intermittent/bugs/2072442.files/fx-tests-feedback.md)

## 63. `fx-tests try --task-ids` prints each task once per failing execution (13)

`fx-tests try --task-ids` prints each task twice (once per failure), and the per-row cap still applies with `--limit 0`, so the 5 lines shown covered only 3 distinct tasks.

- **Question:** What are the distinct failing tasks of one test on a try push?
- **Command:** `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids --limit 0`
- **Expected:** `--limit 0` to list every task ID (and every profile URL) behind the test's row.
- **Got:** 5 task lines then `… 37 more tasks`, even with `--limit 0`. Each task printed twice, so the 5 shown cover only 3 tasks.
- **Suggestion:** One line per distinct task, with its config and its profile URLs, uncapped under `--limit 0`.
- **Example from:** [devtools/devtools/client/application/test/browser/browser_application_panel_open-links.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/application/test/browser/browser_application_panel_open-links.js.files/fx-tests-feedback.md)

## 64. `fx-tests crash` header omits process type and process uptime (13)

`fx-tests crash` showed neither the dump's process type nor its uptime, in default or `--all-threads` output, so telling that a hung child was a GPU process launched at test start required reading `--raw` JSON.

- **Question:** Which process is this dump from, and how long had it been up?
- **Command:** `fx-tests crash LEvE_LI7R_OKi-V60yZP_A.0 C87FB148-8A76-4E15-90E8-D660D120A5DB`
- **Expected:** The process type (main module "Firefox Nightly GPU Helper") and `process_uptime`.
- **Got:** Neither in the default or `--all-threads` output; had to read `--raw` JSON (`modules[main_module].filename`, `process_uptime`).
- **Suggestion:** Add a header line like `Process: GPU (Firefox Nightly GPU Helper), up 32 s`.
- **Example from:** [browser/browser/components/urlbar/tests/quicksuggest/unit/test_suggestBackendMl.js.files/fx-tests-feedback.md](reports/browser/browser/components/urlbar/tests/quicksuggest/unit/test_suggestBackendMl.js.files/fx-tests-feedback.md)

## 65. `task` shows the `killTest` fallout message instead of `Test timed out.` (13)

For a mochitest timeout, `fx-tests task --profiles` shows `[SimpleTest.finish()] No checks actually run.`, which `killTest` logs after the hang. The first TEST-UNEXPECTED-FAIL (`Test timed out.`) is hidden, so it reads like a test that forgot to call `ok()`.

- **Question:** What was the first failure (timeout) of each test in this job?
- **Command:** `fx-tests task Dr9CwGlaSViMZbjcCMwZ7w --profiles`
- **Expected:** `Test timed out.` as each test's message, since it is the first failure.
- **Got:** `[SimpleTest.finish()] No checks actually run. ...` for all three tests.
- **Suggestion:** Show the first TEST-UNEXPECTED-FAIL, using its subtest field (`Test timed out.`) when the message is empty.
- **Example from:** [mac/dom/base/test/test_content_iterator_pre_order.html.files/fx-tests-feedback.md](reports/mac/dom/base/test/test_content_iterator_pre_order.html.files/fx-tests-feedback.md)

## 66. `task --profiles` omits an existing per-test profile for a failure in a green job (13)

`fx-tests task <id> --profiles` listed a failing test with no profile line although its `profile_<test>.json` artifact exists on the task, so the test looked profile-less. Other reports show the same for CRASH tests and `--repeat` jobs.

- **Question:** What is the per-test profile of a failure in a green job?
- **Command:** `fx-tests task KUtuXmw3QX-zY_b-V9HLFQ --profiles`
- **Expected:** The `profile_browser_label_and_icon.js.json` URL under the failing test.
- **Got:** The test listed as FAIL with no profile line, although `public/test_info/profile_browser_label_and_icon.js.json` exists on that task.
- **Suggestion:** List the task's existing `public/test_info/profile_*.json` artifacts instead of relying only on "profile uploaded in" failure messages.
- **Example from:** [mac/browser/components/sessionstore/test/browser_label_and_icon.js.files/fx-tests-feedback.md](reports/mac/browser/components/sessionstore/test/browser_label_and_icon.js.files/fx-tests-feedback.md)

## 67. `try` NEW INTERMITTENTS run count ("2/4") disagrees with `--all-jobs --test` (13)

`fx-tests try <rev>` showed `2/4` for the test, reading as the whole push, but `--all-jobs --test` showed 6 job runs on 3 configs. The count seems to cover only configs where it failed, and the output doesn't say what it counts.

- **Question:** How many times did this test run on the try push, and on which configs did it pass?
- **Command:** `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs`
- **Expected:** The `here` column to count every run of the test on the push, or say which configurations it covers.
- **Got:** `browser_smartblockembeds_trustpanel.js  2/4`; detail lists only the two failing configs, while `--all-jobs --test` shows 6 runs (debug never failed).
- **Suggestion:** Say what the denominator counts, or count all runs of the test on the push.
- **Example from:** [mac/browser/extensions/webcompat/tests/browser/browser_smartblockembeds_trustpanel.js.files/fx-tests-feedback.md](reports/mac/browser/extensions/webcompat/tests/browser/browser_smartblockembeds_trustpanel.js.files/fx-tests-feedback.md)

## 68. `try --test` job counts differ with `--all-jobs` because infra retries are counted (12)

`fx-tests try <rev> --test <path>` showed 4 jobs for some configs, but 3 with `--all-jobs`. The extra one was an infra-retried run with no profile, counted in one view only and not explained in either.

- **Question:** How many jobs ran this test on each config of the try push?
- **Command:** `fx-tests try 2888bcab0070 --test <path>`
- **Expected:** The same count in both views, or a note such as "1 run infra-retried, no profile".
- **Got:** `jobs 4` for linux asan chrome-11 and linux debug chrome-7; `jobs 3` with `--all-jobs`. The extra run was `.0`, Treeherder result `retry`, no profile.
- **Suggestion:** Leave profile-less/infra-retried runs out of the job count, or label them ("1 run infra-retried, no profile").
- **Example from:** [devtools/devtools/client/inspector/animation/test/browser_animation_logic_mutations_fast.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/inspector/animation/test/browser_animation_logic_mutations_fast.js.files/fx-tests-feedback.md)

## 69. `task --messages` shows different messages for the same timeout across jobs (12)

For `test_stepping-17.js`, `fx-tests task` showed "Timed out and was force-killed by the harness" in five jobs but only "Test timed out" in one, even with `--messages`. The profile held the force-killed ERROR there too, so it looked like a different failure mode.

- **Question:** Did this test fail the same way in all six jobs?
- **Command:** `COLUMNS=300 fx-tests task FADMjPT5QXm-7tdUJVLRRQ --limit 0 --messages --quiet`
- **Expected:** The same `... | Timed out and was force-killed by the harness; ...` message as in the other five jobs.
- **Got:** Only `Test timed out` (the `test` marker's message), even with `--messages`.
- **Suggestion:** Pick the test's message consistently, e.g. always include the harness ERROR logged for the test.
- **Example from:** [devtools/devtools/server/tests/xpcshell/test_stepping-17.js.files/fx-tests-feedback.md](reports/devtools/devtools/server/tests/xpcshell/test_stepping-17.js.files/fx-tests-feedback.md)

## 70. No way to find later tests' per-test profiles covering this test's run (12)

A shutdown leak uploads no per-test profile for the failing test. Profiles of later tests in the same manifest and browser session contained its whole run, which was the decisive evidence. Finding them took a shell loop of `fx-tests task --profiles` over 40 tasks.

- **Question:** Which per-test Gecko profile covers this test's run?
- **Command:** `fx-tests test <path> --task-ids --limit 0`
- **Expected:** For failing jobs without their own profile, the per-test profiles of later tests in the same session.
- **Got:** Only task IDs; a loop of `fx-tests task <id> --profiles --quiet` over 40 swr tasks, grepping `profile_*.json` URLs of other tests.
- **Suggestion:** `test <path> --profiles` could list later tests' per-test profiles in the same manifest and session, marked "covers this test's run".
- **Example from:** [browser/browser/components/aiwindow/ui/test/browser/browser_aiwindow_group_tabs_button_model.js.files/fx-tests-feedback.md](reports/browser/browser/components/aiwindow/ui/test/browser/browser_aiwindow_group_tabs_button_model.js.files/fx-tests-feedback.md)

## 71. `fx-tests task` counts hang-kill minidumps as failing executions (12)

For a standalone job where browser_shown.js ran once and timed out, `fx-tests task` reported 9 failing executions. The 9 are the minidumps the harness wrote when force-killing the hung browser's processes.

- **Question:** How many times did this test run in this job, and how many runs failed?
- **Command:** `fx-tests task fx54zwpgQpCPPLSGbcmy9w --profiles --messages`
- **Expected:** One TIMEOUT execution of browser_shown.js (the test ran once).
- **Got:** `CRASH, TIMEOUT — 9 failing executions of 9`, the 9 being minidumps from the harness force-kill (`processing 9 crashes`).
- **Suggestion:** Attach the harness-kill dumps to the single TIMEOUT execution instead of counting each as an execution.
- **Example from:** [browser/browser/base/content/test/browser-tabcrashed/browser_shown.js.files/fx-tests-feedback.md](reports/browser/browser/base/content/test/browser-tabcrashed/browser_shown.js.files/fx-tests-feedback.md)

## 72. `fx-tests try --json` omits the retry's `-2` per-test profile (12)

`fx-tests try --json` lists one `profiles[].testProfiles` URL per task, dropping the `-2` retry profile even when every task failed twice. Only `fx-tests task <id> --profiles` lists both.

- **Question:** What are both per-test profiles (first run and retry) of each failing task on this try push?
- **Command:** `fx-tests try 2888bcab0070 --json`
- **Expected:** Both `profile_browser_dbg-scroll-run-to-completion.js.json` and `...-2.js.json`, since `outcomes.failedTwice` is 21.
- **Got:** One URL per task. `fx-tests task ak4eiOv9SbO_yp9m6cZWFw --profiles` does list both.
- **Suggestion:** List the `-2` retry profile next to the first-run one, labelled as the retry.
- **Example from:** [devtools/devtools/client/debugger/test/mochitest/browser_dbg-scroll-run-to-completion.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/debugger/test/mochitest/browser_dbg-scroll-run-to-completion.js.files/fx-tests-feedback.md)

## 73. `task --messages` shows uncaught rejection message but not its stack (11)

`fx-tests task --messages` printed only the one-line uncaught rejection. The rejection's own stack is only in the live log after TEST-FAIL, so the agent couldn't tell where it was created without downloading a 7 MB log.

- **Question:** Where was this uncaught rejection created?
- **Command:** `fx-tests task Qikk5BwrQDqZgZAkAnPosA --messages`
- **Expected:** The rejection's own stack (`openProtectionsPanel@.../trackingProtection.js:304` via `recvAPICall`).
- **Got:** `1x uncaught rejection: Invalid tab ID: 27` only.
- **Suggestion:** Show the structured-log `stack` field (or the log lines after TEST-FAIL) for uncaught rejection/exception failures.
- **Example from:** [intermittent/browser/extensions/webcompat/tests/browser/browser_smartblockembeds.js.files/fx-tests-feedback.md](reports/intermittent/browser/extensions/webcompat/tests/browser/browser_smartblockembeds.js.files/fx-tests-feedback.md)

## 74. `task --messages` drops the "Passed when the harness reran it" line (11)

`fx-tests task <id> --messages` showed `FAIL — 1 failing execution of 2` with no rerun verdict, while `--profiles` for the same task said all passed on rerun. Its absence reads as the retry failing or never running.

- **Question:** Did this test pass when the harness reran it in this job?
- **Command:** `fx-tests task SFmo9aCKQ96rHSAAo5WL6w --messages`
- **Expected:** The same retry verdict as `fx-tests task SFmo9aCKQ96rHSAAo5WL6w --profiles`.
- **Got:** `FAIL — 1 failing execution of 2` with no "Passed when the harness reran it." line; `--profiles` says "All 3 passed when the harness reran them."
- **Suggestion:** Keep the per-test retry line in every job and output mode; say "not rerun by the harness retry" when it wasn't.
- **Example from:** [browser/browser/base/content/test/browser-popups/browser_popup_blocker.js.four-popups.files/fx-tests-feedback.md](reports/browser/browser/base/content/test/browser-popups/browser_popup_blocker.js.four-popups.files/fx-tests-feedback.md)

## 75. "Failure details not recorded (likely Android...)" label shown for Windows xpcshell failures (11)

`fx-tests test` labels message-less FAILs "likely Android or platform logging issue" even when all 12 failures are on Windows, and `task --profiles` shows no message. The hint sent the agent looking in the wrong place.

- **Question:** What did the harness say when it failed this test?
- **Command:** `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_saveHeapSnapshot_e10s_01.js`
- **Expected:** The harness's reason for the FAIL, or a pointer to the replayed log in the resource-usage profile.
- **Got:** `Failure details not recorded (likely Android or platform logging issue)` for 12 failures that are all on Windows.
- **Suggestion:** Say "no failure line logged; the harness failed it on exit status or missing CHILD-TEST-COMPLETED; read the replayed log in the resource-usage profile".
- **Example from:** [devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_saveHeapSnapshot_e10s_01.js.files/fx-tests-feedback.md](reports/devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_saveHeapSnapshot_e10s_01.js.files/fx-tests-feedback.md)

## 76. `fx-tests test` verdict says perma-fail for a failure that stopped (11)

`fx-tests test` reported "perma-fail" for a test whose `--history` showed 0 failures for the last 6 days, because one config only ran during the failing window. Per-config rates also hid a 4-day 100% failure period.

- **Question:** Is this test still failing, or did the failure stop?
- **Command:** `fx-tests test browser/components/urlbar/tests/unit/test_keywords.js`
- **Expected:** A verdict saying the failure stopped, or "was perma-failing on shippable configs from 09-11 to 09-15".
- **Got:** `Verdict: perma-fail. Never passed on 1 configuration: test-macosx1015-64-shippable-qr/opt-xpcshell-cf (10/10)`.
- **Example from:** [intermittent/browser/components/urlbar/tests/unit/test_keywords.js.files/fx-tests-feedback.md](reports/intermittent/browser/components/urlbar/tests/unit/test_keywords.js.files/fx-tests-feedback.md)

## 77. `test --coverage` can't split per-config runs/failures before and after a date (11)

To show per-config runs and failures before and after a fix landed, the agent ran `fx-tests test <path> --coverage` twice (full window and `--since 10`) and subtracted per config by hand.

- **Question:** How many runs did the failing configs have before the fix date and since, with how many failures in each?
- **Command:** `fx-tests test <path> --since 10 --coverage --limit 0`
- **Expected:** Per-config pass/fail counts on each side of the fix date, in one call.
- **Got:** Only whole-window counts; the "before" side had to be computed by subtracting the `--since 10` output from the full-window output per config.
- **Suggestion:** A `--split <date>` (or `--history --config <substring>`) giving per-config pass/fail counts on each side of a date.
- **Example from:** [mac/devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-sidebar.js.files/fx-tests-feedback.md](reports/mac/devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-sidebar.js.files/fx-tests-feedback.md)

## 78. `crash` shows no crash annotations such as `StorageConnectionNotClosed` (11)

`fx-tests crash <task> <dump>` prints only the stack in every output mode. The `.extra` annotations are missing, e.g. `StorageConnectionNotClosed`, which names the unclosed database.

- **Question:** What did the crashed test print (crash annotation / "Storage connection not closed: X")?
- **Command:** `fx-tests crash WhKsWGhIQmysId7--mM6OA.0 25f6f822-f5ea-4d49-a452-e40e1c303d76`
- **Expected:** Crash annotations, here `StorageConnectionNotClosed`, which names the database.
- **Got:** Stack only; no annotations in any output mode (also `--json`, `--raw`).
- **Suggestion:** Print the dump's `.extra` annotations (e.g. `StorageConnectionNotClosed`, `MozCrashReason`, `RemoteType`).
- **Example from:** [browser/browser/components/pagedata/tests/unit/test_schemaorg_parse.js.files/fx-tests-feedback.md](reports/browser/browser/components/pagedata/tests/unit/test_schemaorg_parse.js.files/fx-tests-feedback.md)

## 79. `intermittent --bug` Occurrences table truncates platform and job name (10)

`fx-tests intermittent --bug` cuts the platform and job-name columns in the Occurrences table (`macosx1500-…`, `mochitest-r…`) even with `--limit 0`, hiding the config that distinguishes occurrences; full values are only in `--json`.

- **Question:** Which config and chunk is each annotated job?
- **Command:** `fx-tests intermittent --bug 2022292 --limit 0`
- **Expected:** Full platform and job name per occurrence, to tell `-vms` from hardware and `-shippable` from not.
- **Got:** `macosx1500-…  opt    mochitest-r…`; job name and platform cut even with `--limit 0`.
- **Suggestion:** Print the full `platform` and `testSuite` in occurrence rows, or honour COLUMNS.
- **Example from:** [intermittent/bugs/2022292.files/fx-tests-feedback.md](reports/intermittent/bugs/2022292.files/fx-tests-feedback.md)

## 80. `test --profiles` lists resource-usage profiles, not per-test profiles of failures (10)

`fx-tests test <path> --profiles` printed only the resource-usage URL for each failing task, even though per-test profiles existed; `fx-tests task <id> --profiles` per task did show them.

- **Question:** What are the per-test profile URLs of this test's FAIL/TIMEOUT occurrences?
- **Command:** `fx-tests test <path> --profiles --limit 0`
- **Expected:** For each failing task, the per-test profile URL (`profile_test_ext_dnr_download.js.json`) when one was uploaded.
- **Got:** only the `resource-usage:` URL for every task, FAIL and TIMEOUT tasks included.
- **Suggestion:** List per-test profile URLs of failing runs, or say none were uploaded (e.g. "per-test profiles: 0 of N").
- **Example from:** [intermittent/toolkit/components/extensions/test/xpcshell/test_ext_dnr_download.js.files/fx-tests-feedback.md](reports/intermittent/toolkit/components/extensions/test/xpcshell/test_ext_dnr_download.js.files/fx-tests-feedback.md)

## 81. `task --profiles` on Android doesn't point to the logcat artifact (10)

On an Android xpcshell timeout, `fx-tests task --profiles` said only that no per-test profile existed. It didn't mention the logcat, which holds every assertion, so the report wrongly concluded that Android timeouts have no log output.

- **Question:** What did the test log before an Android timeout whose log replay is empty?
- **Command:** `fx-tests task SbqcbHitRpWx0doA_3n1Sg --profiles`
- **Expected:** A pointer to the task's `public/test_info/logcat-emulator-5554.log`, which holds the test's `test_status` lines.
- **Got:** Only "No failing test named a per-test profile in this job"; the logcat is not mentioned.
- **Suggestion:** On Android jobs, print the logcat artifact URL next to the resource-usage profile.
- **Example from:** [intermittent/toolkit/components/extensions/test/xpcshell/test_ext_csp_upgrade_requests.js.files/fx-tests-feedback.md](reports/intermittent/toolkit/components/extensions/test/xpcshell/test_ext_csp_upgrade_requests.js.files/fx-tests-feedback.md)

## 82. `task` on Android xpcshell doesn't show each test's service slot (10)

For a `Could not kill left-over process` failure, the agent needed which `XpcshellTestRunnerService$iN` slot each test held. `fx-tests task --profiles` shows nothing about slots, so it took joining profile markers in a script and deducing slots by elimination.

- **Question:** Which slot did the failing test hold, and which slots did the tests running next to it hold?
- **Command:** `fx-tests task QI7tqM3WTWSvnUcSnUah6A --profiles`
- **Expected:** The slot of each test (from its `launch_application` marker), including tests that never launched.
- **Got:** Nothing about slots.
- **Suggestion:** Show each Android test's slot next to it, or "none launched".
- **Example from:** [devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_takeCensus_04.js.files/fx-tests-feedback.md](reports/devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_takeCensus_04.js.files/fx-tests-feedback.md)

## 83. `test --durations` mixes sequential retries with parallel-phase runs (10)

`fx-tests test --durations` gives one distribution per config. For a parallel-only failure, nearly all passes are sequential harness retries. The median suggests the test is fast, but it almost never finishes within 120 s in the parallel phase.

- **Question:** How long does this test take in the parallel phase?
- **Command:** `fx-tests test <path> --durations`
- **Expected:** Durations split by execution mode, or flagged when most passing runs are sequential retries.
- **Got:** `test-macosx1500-aarch64/debug-xpcshell 250 runs, median 15687 ms`: 243 of those 250 passes are sequential retries.
- **Suggestion:** Split durations by execution mode (parallel / sequential).
- **Example from:** [mac/toolkit/components/translations/tests/unit/test_telemetry_sampling.js.files/fx-tests-feedback.md](reports/mac/toolkit/components/translations/tests/unit/test_telemetry_sampling.js.files/fx-tests-feedback.md)

## 84. `test --task-ids` has no per-task "retry passed" marker (9)

To find jobs where the first run failed and the harness retry passed, the agent used `fx-tests test --task-ids`. The `×2` mark didn't track failing executions, and `--executions` gives only totals, so it ran `fx-tests task` on candidates one by one.

- **Question:** Which jobs had a harness retry of this test that passed?
- **Command:** `fx-tests test toolkit/content/tests/browser/browser_aboutsupport_codec_extension_link.js --harness mochitest --task-ids --limit 0 --since 2`
- **Expected:** A way to list jobs where the first run failed and the retry passed.
- **Got:** Tasks without `×2` that `fx-tests task` reported as "2 failing executions of 2"; `--executions` gives only totals (187 jobs, 135 with more than one failure).
- **Suggestion:** A per-task "retry passed" flag in `--task-ids`.
- **Example from:** [intermittent/toolkit/content/tests/browser/browser_aboutsupport_codec_extension_link.js.files/fx-tests-feedback.md](reports/intermittent/toolkit/content/tests/browser/browser_aboutsupport_codec_extension_link.js.files/fx-tests-feedback.md)

## 85. `fx-tests try --messages` prints no failure message for NEW INTERMITTENTS rows (9)

`fx-tests try --messages` prints failure messages for PERMA-FAILS rows but not for NEW INTERMITTENTS rows, so the agent had to run `fx-tests task` per task to learn how the test failed.

- **Question:** What is the failure message of this test on my try push?
- **Command:** `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --messages --task-ids --full-messages --limit 0`
- **Expected:** The failure message(s) under the `browser_storage_cookies_sort.js` row of NEW INTERMITTENTS, as PERMA-FAILS rows print.
- **Got:** Config, central rate and task IDs only; no message, even with `--messages`.
- **Suggestion:** Print messages for every row with `--messages`, and also honour `--messages` with `--test <path>`.
- **Example from:** [devtools/devtools/client/storage/test/browser_storage_cookies_sort.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/storage/test/browser_storage_cookies_sort.js.files/fx-tests-feedback.md)

## 86. `errors --message` with a literal error code matches nothing (stored as `<num>`) (9)

`fx-tests errors --message "Error:-2147009284"`, copied from a log, matched nothing because stored messages are normalized to `Error:-<num>` and the query isn't. The tool still asserted the absence was real.

- **Question:** Which tests logged "Failed to launch tab subprocess @CreateProcess (Error:-2147009284)"?
- **Command:** `fx-tests errors --harness xpcshell --message "Error:-2147009284"`
- **Expected:** The 75 occurrences in 19 tests that exist in that file.
- **Got:** "No markers matched. … this is a filter with no matches rather than an empty file."
- **Suggestion:** Normalize the query with the same rules (or match both forms), or say numbers are stored as `<num>` when a numeric query matches nothing.
- **Example from:** [browser/browser/components/places/tests/unit/test_invalid_defaultLocation.js.files/fx-tests-feedback.md](reports/browser/browser/components/places/tests/unit/test_invalid_defaultLocation.js.files/fx-tests-feedback.md)

## 87. `task` and `test --task-ids` omit minidump IDs of CRASH rows (9)

`fx-tests task <id> --profiles` lists a CRASH with its signature but no minidump ID, in text or `--json`, so `fx-tests crash` can't be reached without listing the task's artifacts by hand.

- **Question:** What is the minidump ID of the crash this job recorded for my test?
- **Command:** `fx-tests task AHcMg5k-SDmfpThFMLCKAw --profiles`
- **Expected:** The CRASH row (`@ mozilla::(anonymous namespace)::RunWatchdog`) with its minidump ID and the `profile_shutdown_hang_<pid>.json` URL.
- **Got:** No dump ID anywhere; `--json` only has `statusCounts.CRASH: 1`. `test --task-ids` gave no minidump ID either, though the .dmp was uploaded.
- **Suggestion:** Print the dump ID next to each CRASH row, and list `profile_shutdown_hang_*.json` artifacts.
- **Example from:** [devtools/devtools/client/aboutdebugging/test/browser/browser_aboutdebugging_thisfirefox.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/aboutdebugging/test/browser/browser_aboutdebugging_thisfirefox.js.files/fx-tests-feedback.md)

## 88. `test --bugs` cannot find bugs whose summary names the failure message (9)

`fx-tests test --bugs` only looks for bugs naming the test path, and printed no Bugs section at all. The covering bug named the failure message ("Network cache reported memory consumption is not at 0") instead.

- **Question:** Which bug covers this failure?
- **Command:** `fx-tests test browser/components/mozcachedohttp/test/unit/test_url_parsing.js --bugs`
- **Expected:** A Bugs section, ideally with bugs whose summary matches the failure message (here bug 1994039).
- **Got:** The ordinary report, with no Bugs section at all, so it is unclear whether the search ran.
- **Suggestion:** Also search bug summaries for the test's failure messages or crash signatures, and print "none" when nothing matches.
- **Example from:** [browser/browser/components/mozcachedohttp/test/unit/test_url_parsing.js.files/fx-tests-feedback.md](reports/browser/browser/components/mozcachedohttp/test/unit/test_url_parsing.js.files/fx-tests-feedback.md)

## 89. `fx-tests test` silently excludes test-verify jobs, reports "passing" (9)

`fx-tests test` said "passing" with 0 failures for a test whose only failures in the window were 9 test-verify jobs. Nothing says TV jobs are excluded; only `fx-tests intermittent --bug` found them.

- **Question:** Did this test fail in the window, including its test-verify failures?
- **Command:** `fx-tests test browser/components/urlbar/tests/browser-results/browser_retainedResultsOnFocus.js`
- **Expected:** The 9 test-verify failures of 2026-09-07, or a line saying test-verify jobs are not counted.
- **Got:** `15,870 runs 15,870 pass (100.00%) 0 fail`, `Verdict: passing`, and every `--history` day at 0 fail.
- **Suggestion:** Include test-verify runs, or print a line saying test-verify jobs are not counted.
- **Example from:** [intermittent/browser/components/urlbar/tests/browser-results/browser_retainedResultsOnFocus.js.files/fx-tests-feedback.md](reports/intermittent/browser/components/urlbar/tests/browser-results/browser_retainedResultsOnFocus.js.files/fx-tests-feedback.md)

## 90. `fx-tests test` has no reftest/crashtest data (9)

`fx-tests test` on a reftest path reports the path as absent from the xpcshell and mochitest data, and `--harness` only takes xpcshell|mochitest, so reftests have no per-config rates, history or failing task IDs.

- **Question:** What are the per-config rates, history and failing task IDs for this reftest?
- **Command:** `fx-tests test layout/reftests/bugs/501627-1.html`
- **Expected:** Rates per config, history, failing task IDs for a reftest.
- **Got:** "No test path in the xpcshell and mochitest 21-day data contains ...". `--harness` only takes xpcshell|mochitest.
- **Suggestion:** Index the reftest/crashtest harness in `fx-tests test`, or at least say explicitly that reftests are not covered.
- **Example from:** [intermittent/layout/reftests/bugs/501627-1.html.files/fx-tests-feedback.md](reports/intermittent/layout/reftests/bugs/501627-1.html.files/fx-tests-feedback.md)

## 91. `task` shows no per-execution durations (9)

`fx-tests task <id> --json` gives no duration for each execution of a failing test, so it can't show whether the harness retry was much faster. The durations are in the resource-usage profile's `test` markers.

- **Question:** How long did each execution of this test take in this job (first run vs harness retry)?
- **Command:** `fx-tests task Y9w02fRyR9qdnDxAkYA-WQ.0 --json`
- **Expected:** A duration for each execution in `failures[]` (first run 57.6 s FAIL, retry 19.1 s PASS).
- **Got:** No durations. Had to load the resource-usage profile and read the `test` markers.
- **Suggestion:** Add `executions: [{status, durationMs, retry: bool}]` for each failing test.
- **Example from:** [browser/browser/extensions/formautofill/test/browser/browser_managePersonalInfoSubpage.js.files/fx-tests-feedback.md](reports/browser/browser/extensions/formautofill/test/browser/browser_managePersonalInfoSubpage.js.files/fx-tests-feedback.md)

## 92. `fx-tests test` has no gtest data and doesn't say gtest is unindexed (8)

`fx-tests test` on a gtest (file path or `Suite.Case` name) returns the generic "No test path ... contains" error, which reads as if the test never ran; no failure rate per config is available.

- **Question:** How often does this gtest fail per config?
- **Command:** `fx-tests test dom/media/gtest/TestAudioTrackGraph.cpp`
- **Expected:** Rates per config for a gtest failing ~1.4% on macOS, or a message saying gtest is not covered.
- **Got:** "No test path in the xpcshell and mochitest 21-day data contains ..." — reads as if the test did not run, when the harness is simply not indexed.
- **Suggestion:** Say gtest is not covered, or index gtests from the per-test `test` markers already in resource-usage profiles.
- **Example from:** [intermittent/bugs/2003533.files/fx-tests-feedback.md](reports/intermittent/bugs/2003533.files/fx-tests-feedback.md)

## 93. `task --profiles` doesn't say which execution an unsuffixed profile belongs to (8)

With "2 failing executions of 2", `fx-tests task --profiles` listed one unsuffixed profile, which reads as the first run's. It was the retry's: the first upload failed (Out of Memory), so the retry took the unsuffixed name.

- **Question:** Which run of the job is this profile from?
- **Command:** `fx-tests task b_7uPWjIQE-MqD-D_HpNRg --profiles --limit 0`
- **Expected:** Two profiles, or a note that one is missing, and which run the listed one belongs to.
- **Got:** one profile, `profile_browser_ext_request_permissions.js.json`, without a `-2` suffix, so it reads as the first run's.
- **Suggestion:** Label each profile with its execution (first/retry) and report failed profile uploads.
- **Example from:** [mac/browser/components/extensions/test/browser/browser_ext_request_permissions.js.files/fx-tests-feedback.md](reports/mac/browser/components/extensions/test/browser/browser_ext_request_permissions.js.files/fx-tests-feedback.md)

## 94. `crash` can't tell which harness-kill dump is the parent process (8)

For an `application timed out` hang with 10 minidumps, every `fx-tests crash <task> <dump>` call said "records no crashing thread". Finding the parent process took `--all-threads` on all 10 dumps.

- **Question:** Which of the harness-kill dumps is the parent process, and what was its main thread doing?
- **Command:** `fx-tests crash WV24He4bR2KQDVcooD68UA.0 <dumpId>`
- **Expected:** The process type of each dump, and the main thread's stack of the parent.
- **Got:** Every one of 10 dumps: "records no crashing thread". The workaround, `--all-threads --frames 25` on all 10, printed 7,500 lines.
- **Suggestion:** For a dump with no crashing thread, print the process type (or thread count) and thread #0's stack by default.
- **Example from:** [browser/browser/components/aiwindow/ui/test/browser/browser_smartwindow_recentchats.js.files/fx-tests-feedback.md](reports/browser/browser/components/aiwindow/ui/test/browser/browser_smartwindow_recentchats.js.files/fx-tests-feedback.md)

## 95. `task --profiles` gives no memory peak/slope summary from the resource-usage profile (8)

To compare a finished job with jobs killed for memory, the agent needed system memory over time and its peak. `fx-tests task --profiles` doesn't summarize it, and profiler-cli has no counter for it, so it took a Python script over Memory markers.

- **Question:** How much system memory did this job use over time, and when did it peak?
- **Command:** *(no fx-tests command answers this)*
- **Expected:** A peak and a start-to-end slope of the Memory track, next to the CPU summary, in `fx-tests task --profiles`.
- **Got:** No summary from fx-tests; profiler-cli `counter list`: "No counters in this profile", since memory is stored as markers.
- **Suggestion:** Print memory peak and slope per job in `task --profiles`, next to the CPU summary.
- **Example from:** [intermittent/bugs/1977276.files/fx-tests-feedback.md](reports/intermittent/bugs/1977276.files/fx-tests-feedback.md)

## 96. `test --issue N` selects a different row than printed Issues row N (7)

`fx-tests test --task-ids --issue 4` returned the tasks of the row printed 5th, and `--issue 5` returned row 4. The two rows have close counts, so the two orderings probably use different sort keys or tie-breaks. Only the `Task IDs (issue N: ...)` header showed the mismatch.

- **Question:** Which tasks belong to Issues row 4 of this test?
- **Command:** `fx-tests test browser/components/customizableui/test/browser_876926_customize_mode_wrapping.js --task-ids --issue 4 --day 2026-09-21`
- **Expected:** Tasks of row 4: `482x FAIL leaked 1 window(s) until shutdown [url = about:blank]`.
- **Got:** `Task IDs (issue 4: FAIL leaked window until shutdown [url = chrome://browser/content/browser.xhtml])`, which is row 5 in the printed list.
- **Suggestion:** Use the same ordering as the Issues list, or accept a message substring for `--issue`.
- **Example from:** [browser/browser/components/customizableui/test/browser_876926_customize_mode_wrapping.js.files/fx-tests-feedback.md](reports/browser/browser/components/customizableui/test/browser_876926_customize_mode_wrapping.js.files/fx-tests-feedback.md)

## 97. `test --task-ids` doesn't mark tasks with per-test profiles (TSan never has one) (7)

`fx-tests test --task-ids` listed 80 tasks, 72 of them TSan, with no profile indication. The first 5 `task --profiles` calls, all on TSan, found no per-test profile; only ASan and debug-standalone tasks had one.

- **Question:** Which failing tasks of this test have a per-test profile I can load?
- **Command:** `fx-tests test browser/components/sessionstore/test/browser_tab_groups_saved.js --task-ids --limit 0`
- **Expected:** Tasks with a per-test profile marked, or a note that linux2404-64-tsan never uploads them.
- **Got:** 80 task IDs; each TSan `task --profiles` said "No failing test named a per-test profile in this job".
- **Suggestion:** Mark tasks naming a per-test profile in `--task-ids`/`--profiles`, or note configs that never upload them.
- **Example from:** [intermittent/browser/components/sessionstore/test/browser_tab_groups_saved.js.files/fx-tests-feedback.md](reports/intermittent/browser/components/sessionstore/test/browser_tab_groups_saved.js.files/fx-tests-feedback.md)

## 98. `fx-tests test --durations` omits each config's effective timeout (7)

`fx-tests test --durations` shows per-config durations but not the effective timeout. A 95 s median on macosx1015 debug next to a 30 s-limit failure on macosx1500 looked contradictory until the agent read the taskcluster timeoutfactor by hand.

- **Question:** What timeout did this test run under on this config?
- **Command:** `fx-tests test browser/components/urlbar/tests/unit/test_search_suggestions.js --durations`
- **Expected:** Next to per-config durations, the effective timeout (30 s x timeoutfactor x requesttimeoutfactor), e.g. "median 28.1 s against a 30 s limit".
- **Got:** Durations only.
- **Suggestion:** Print each config's effective timeout (base x timeoutfactor x requesttimeoutfactor) next to the durations.
- **Example from:** [intermittent/browser/components/urlbar/tests/unit/test_search_suggestions.js.files/fx-tests-feedback.md](reports/intermittent/browser/components/urlbar/tests/unit/test_search_suggestions.js.files/fx-tests-feedback.md)

## 99. `test --task-ids --issue N` reprints the whole summary on every call (7)

Getting task IDs per failure mode with `fx-tests test <path> --task-ids --limit 0 --issue N` reprinted the verdict, full config table and Issues list each time. Four failure modes meant four full summaries.

- **Question:** What are the failing tasks and configs of each failure mode?
- **Command:** `fx-tests test netwerk/test/unit/test_dictionary_replacement.js --task-ids --limit 0 --issue 4`
- **Expected:** Only the task IDs of that issue, since the summary was already read.
- **Got:** The verdict, the full failing-configurations table and the whole Issues list again before the task IDs, on every call.
- **Suggestion:** Offer a way to print only the requested section (e.g. `--only history`).
- **Example from:** [intermittent/netwerk/test/unit/test_dictionary_replacement.js.available-dictionary-null.files/fx-tests-feedback.md](reports/intermittent/netwerk/test/unit/test_dictionary_replacement.js.available-dictionary-null.files/fx-tests-feedback.md)

## 100. No per-push/revision pass-fail breakdown for a test (7)

`fx-tests test` can't give a test's outcome per push or revision range, so checking whether it passed while a regression was live needed one slow `fx-tests try ... --all-jobs` per push.

- **Question:** Did this test pass on any push while the regression was live (autoland pushes 277813-277824)?
- **Command:** `fx-tests try <rev> --project autoland --test <path> --all-jobs`
- **Expected:** Per-push pass/fail for the test across the revision range, in one command.
- **Got:** One run per push, each reading every test job's profile (slow, several minutes per push).
- **Suggestion:** `fx-tests test <path> --revisions <from>..<to>` (or `--push-range`) giving per-push pass/fail.
- **Example from:** [devtools/devtools/server/tests/browser/browser_perf-01.js.files/fx-tests-feedback.md](reports/devtools/devtools/server/tests/browser/browser_perf-01.js.files/fx-tests-feedback.md)

## 101. `intermittent --bug` history summed over trees; no per-tree breakdown (7)

`fx-tests intermittent --bug` found no trunk annotations in the default window, and its `history` field is summed over trees, so it couldn't show that mozilla-esr153 kept failing after the trunk fix.

- **Question:** Is this bug still being starred anywhere after the trunk fix?
- **Command:** `fx-tests intermittent --bug 2019094`
- **Expected:** A hint that other trees have annotations in that window (mozilla-esr153 had 8).
- **Got:** exit 2, "no sheriff annotations for bug 2019094 on trunk between 2026-09-16 and 2026-09-22", with a generic "widen with --since, or try --tree all" hint.
- **Suggestion:** Show a per-tree x per-day count, and say how many annotations other trees have in the window.
- **Example from:** [intermittent/toolkit/content/tests/browser/browser_findbar_marks.js.files/fx-tests-feedback.md](reports/intermittent/toolkit/content/tests/browser/browser_findbar_marks.js.files/fx-tests-feedback.md)

## 102. `fx-tests errors --message` can't find multi-line console.error ("remote browser crashed") (7)

`fx-tests errors --message "remote browser crashed"` found nothing: the console.error's first line is empty and the text sits in separate `output` lines, so tab crashes can't be found by their text.

- **Question:** Which tests/jobs print "remote browser crashed while on about:blank"?
- **Command:** `fx-tests errors --message "remote browser crashed" --group-by test`
- **Expected:** The tests whose logs contain that console.error.
- **Got:** "No markers matched." In the profile, the `console.error` marker has an empty Message; the text is in the following `output` markers.
- **Suggestion:** Join a console.error's continuation lines into its message.
- **Example from:** [devtools/devtools/client/webconsole/test/browser/browser_webconsole_stubs_page_error.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/webconsole/test/browser/browser_webconsole_stubs_page_error.js.files/fx-tests-feedback.md)

## 103. `fx-tests crash` says an empty dump "is not there" and blames expiry (7)

`fx-tests crash` reported a one-day-old minidump as missing and possibly expired, while the artifact existed (HTTP 200) but was only 61 bytes long, sending the agent looking for expiry.

- **Question:** What is the parent process's hang stack in this dump?
- **Command:** `fx-tests crash TlVBQEElQMeu8rl9OAcDoQ 72505f1f-74cb-4d47-8b6d-24f36bade9ba`
- **Expected:** The parent's hang stack, or a message saying the dump is empty or truncated.
- **Got:** `no minidump ... the artifact is not there ... Taskcluster expires artifacts`, though the artifact exists (61 bytes) and the task is one day old.
- **Suggestion:** Distinguish an empty/truncated dump artifact from a missing or expired one in the message.
- **Example from:** [intermittent/browser/components/sessionstore/test/browser_startup_timeline_telemetry.js.files/fx-tests-feedback.md](reports/intermittent/browser/components/sessionstore/test/browser_startup_timeline_telemetry.js.files/fx-tests-feedback.md)

## 104. `task --profiles` omits the shutdown hang and its profile (6)

`fx-tests task <id> --profiles` lists only the test failures and their per-test profiles. It leaves out the job's `shutdown hang` failure and its `profile_shutdown_hang_1622.json`, the only profile covering the whole browser session.

- **Question:** What else failed in this job, including shutdown hangs and their profiles?
- **Command:** `fx-tests task e7OWzSu9TZuu2o2YgQY0YQ --profiles`
- **Expected:** The job's `TEST-UNEXPECTED-FAIL | shutdown hang | profile uploaded in profile_shutdown_hang_1622.json`, with its profile URL.
- **Got:** Only the 4 test failures and their per-test profiles. Neither the shutdown hang nor `profile_shutdown_hang_1622.json` is mentioned.
- **Suggestion:** A "shutdown / harness failures" section listing shutdown hangs and their profile URLs, and other artifacts (screenshots, scope-variables dumps).
- **Example from:** [devtools/devtools/client/debugger/test/mochitest/browser_dbg-overrides-original.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/debugger/test/mochitest/browser_dbg-overrides-original.js.files/fx-tests-feedback.md)

## 105. `task` says "Passed when the harness reran it" when the rerun failed (6)

In test-verify jobs, `fx-tests task <id>` reports "Passed when the harness reran it" although the retry failed too (a `-2` profile exists); a passing iteration inside the retry seems to be read as the retry passing.

- **Question:** Did the harness retry of this test pass or fail?
- **Command:** `fx-tests task cOrphxTIT4ehoo6XfZEgcA --profiles --messages`
- **Expected:** The retry reported as failed: a `-2` profile exists and the retry's runs show PASS then FAIL (timeout).
- **Got:** "FAIL — 2 failing executions of 4 / Passed when the harness reran it."
- **Suggestion:** Under repeat/test-verify, report "passed on retry" only when the retry had no failing execution.
- **Example from:** [intermittent/docshell/test/browser/browser_bug503832.js.files/fx-tests-feedback.md](reports/intermittent/docshell/test/browser/browser_bug503832.js.files/fx-tests-feedback.md)

## 106. `intermittent --bug` doesn't flag backfill/retrigger jobs, inflating counts (6)

`fx-tests intermittent --bug 1667271` listed 72 annotated jobs with nothing marking backfills or retriggers; `--history` showed a spike that looked like a regression. 55 of 58 jobs on two days were one sheriff backfill.

- **Question:** How many of the annotated jobs are retriggers or backfills of one scheduling event?
- **Command:** `fx-tests intermittent --bug 1667271 --since 21 --limit 0`
- **Expected:** A way to see that 55 of 58 jobs were one backfill (`action: backfill-task`) and 9 more retriggers.
- **Got:** One row per job, job name cut off (`geckoview-m…`); nothing marked backfill/retrigger. `--history` showed a 36/22 spike on 2026-09-09/10.
- **Suggestion:** Add an origin column (decision/retrigger/backfill from `tags.action`) and a count of distinct pushes.
- **Example from:** [intermittent/bugs/1667271.files/fx-tests-feedback.md](reports/intermittent/bugs/1667271.files/fx-tests-feedback.md)

## 107. `task --profiles` shows no profile sizes, though many are too large to load (6)

`fx-tests task --profiles` listed a per-test profile URL without its size. The profile was 62 MB gzipped (875 MB decoded) and `profiler-cli load` died silently on it. Finding a loadable one meant HEAD requests on 57 tasks.

- **Question:** How big is this per-test profile, before I load it?
- **Command:** `fx-tests task FJATl84VR8iclfuypyQVbA --profiles`
- **Expected:** Some hint that `profile_marPostUpdateEnvironment.js.json` is 62 MB gzipped / 875 MB decoded.
- **Got:** Just the URL, although the resource-usage profile's `artifact` marker already recorded "profile_marPostUpdateEnvironment.js.json — 471MB".
- **Suggestion:** Print the size next to each profile URL, with a warning above ~500 MB decoded.
- **Example from:** [mac/toolkit/mozapps/update/tests/unit_update_binary/marPostUpdateEnvironment.js.files/fx-tests-feedback.md](reports/mac/toolkit/mozapps/update/tests/unit_update_binary/marPostUpdateEnvironment.js.files/fx-tests-feedback.md)

## 108. No `task --test <path> --log` to print one test's log lines with timestamps (6)

To see when `asyncConnect` returned and when the session became ready in each of seven failing jobs, the agent had to load each resource-usage profile and filter the test's INFO lines with a script. No fx-tests command prints one test's log lines.

- **Question:** What happened to this test in each failing job, in time order?
- **Command:** *(no fx-tests command answers this)*
- **Expected:** `fx-tests task <taskId> --test <path> --log` printing that test's log lines with job-relative timestamps.
- **Got:** No such option; needed a script loading each task's resource-usage profile, one load per job.
- **Suggestion:** Add `task --test <path> --log`, or the same for all failing tasks of one test.
- **Example from:** [mac/netwerk/test/unit/test_webtransport_stop_sending.js.files/fx-tests-feedback.md](reports/mac/netwerk/test/unit/test_webtransport_stop_sending.js.files/fx-tests-feedback.md)

## 109. `try --project autoland` calls the push's own failures "Pre-existing" (5)

For an autoland push, `fx-tests try` counted the push's own failing jobs as central history. It said "probably not yours" about a push that was then backed out, pointing away from the culprit.

- **Question:** Is this failure new with this push?
- **Command:** `fx-tests try 82333bc598c6 --project autoland --harness mochitest --perma-only --task-ids`
- **Expected:** A verdict that the failure is new with this push, since its failures are the only ones in the window.
- **Got:** "Pre-existing: central already fails the same way on the 3 configs ... (6 times in 21 days) — probably not yours." All 6 are this push's own tasks.
- **Suggestion:** Exclude the push's own jobs from the central history it compares against.
- **Example from:** [devtools/devtools/client/webconsole/test/browser/browser_webconsole_sourcemap_invalid.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/webconsole/test/browser/browser_webconsole_sourcemap_invalid.js.files/fx-tests-feedback.md)

## 110. `intermittent --bug` shows only the starred line, no preceding log context (5)

For bug 915212, every occurrence in `fx-tests intermittent --bug` shows only the generic starred line, so all 55 look identical. The cause is in server stderr a few lines earlier, and 32 of 55 were mis-stars of bug 1976612.

- **Question:** Which failure modes are behind one bug's annotations, and which belong to another bug?
- **Command:** `fx-tests intermittent --bug 915212 --since 30 --limit 0`
- **Expected:** Occurrences grouped by the discriminating log line before the starred one (e.g. `could not start server on port N: ...`).
- **Got:** Only `TEST-UNEXPECTED-FAIL | runtests.py | Timed out while waiting for server startup.` per occurrence. Workaround: downloaded 175 logs (45 MB).
- **Suggestion:** Add `--context <n>` or `--log-grep <re>` on `intermittent --bug`, plus a hint when context matches another bug's summary.
- **Example from:** [intermittent/bugs/915212.files/fx-tests-feedback.md](reports/intermittent/bugs/915212.files/fx-tests-feedback.md)

## 111. Per-config fail rate counts sequential retries, hiding a near-perma parallel failure (5)

The `fx-tests test` per-config rate counts xpcshell sequential retries (which pass) in the denominator. A parallel-phase failure rate of 97.2% shows as 49.3%, and each config needs a separate `--executions` call to see it.

- **Question:** What is this test's real failure rate in the parallel phase on each config?
- **Command:** `fx-tests test toolkit/components/translations/tests/unit/test_telemetry_sampling.js`
- **Expected:** With the verdict "Fails almost only in parallel", the per-config table to show the parallel-phase rate too.
- **Got:** `test-macosx1500-aarch64/debug-xpcshell 49.3%`, while the parallel-phase rate is 97.2% (243/250).
- **Suggestion:** Add a parallel-only rate column (or a jobs column) to the per-config table.
- **Example from:** [mac/toolkit/components/translations/tests/unit/test_telemetry_sampling.js.files/fx-tests-feedback.md](reports/mac/toolkit/components/translations/tests/unit/test_telemetry_sampling.js.files/fx-tests-feedback.md)

## 112. `try` warns about jobs killed at max duration without naming them (5)

`fx-tests try` reported only a count of killed jobs on stderr, with nothing in JSON, so the agent couldn't tell which jobs were missing or whether their failures showed up elsewhere.

- **Question:** Which try jobs were killed at their maximum duration, and do their failures show up anyway?
- **Command:** `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs --json --limit 0`
- **Expected:** The jobs behind "5 of 236 jobs were killed for exceeding their maximum duration" named, in text and JSON.
- **Got:** Only a count on stderr, nothing in the JSON.
- **Suggestion:** Name the task IDs and job names of killed, unread and unblamed jobs in text and JSON.
- **Example from:** [mac/todo.files/fx-tests-feedback.md](reports/mac/todo.files/fx-tests-feedback.md)

## 113. `intermittent --bug` occurrences show push time, not job start time (5)

`fx-tests intermittent --bug` occurrence rows give only push time, so retriggers of an old push are dated hours before they ran, and an outage-like episode can't be dated. No revision column either.

- **Question:** When did each annotated failure happen, to date the start and end of an episode?
- **Command:** `fx-tests intermittent --bug 2069174 --since 30 --tree all --limit 0`
- **Expected:** When each failure happened (job start time).
- **Got:** Push time only; retriggers show their push's time, e.g. pushes at 2026-09-03 20:04 whose jobs ran at 2026-09-04 07:30.
- **Suggestion:** Add job start time and revision columns to the occurrences table.
- **Example from:** [intermittent/bugs/2069174.files/fx-tests-feedback.md](reports/intermittent/bugs/2069174.files/fx-tests-feedback.md)

## 114. `task` on a confirm-failure (-cf) job doesn't name the job it confirms (5)

`fx-tests task` on a `-cf` job says nothing about the original failing job. The agent had to follow the task's `extra.parent` action task and its `ACTION_TASK_ID` env by hand.

- **Question:** Which job did this confirm-failure (`-cf`) job confirm?
- **Command:** `fx-tests task DVp16B5WSkWdLSb3Ww4A1Q --profiles`
- **Expected:** The original failing job, since a `-cf` job only reruns a test that failed elsewhere.
- **Got:** Nothing about the parent.
- **Suggestion:** Print "confirm-failure of <taskId>" in the `fx-tests task` header for a `-cf` job.
- **Example from:** [mac/devtools/client/shared/components/test/chrome/test_tree_14.html.files/fx-tests-feedback.md](reports/mac/devtools/client/shared/components/test/chrome/test_tree_14.html.files/fx-tests-feedback.md)

## 115. `fx-tests task` ignores "unknown test url" failures instead of attributing them to the running test (4)

Failures logged as "unknown test url" are not attributed to the running test: `fx-tests task` said the job had no test-level failure, and `fx-tests test` counted 1 fail where sheriffs annotated 34.

- **Question:** Which test is behind bug 2056104, and how often does it fail per run?
- **Command:** `fx-tests task TOSzChswR5ePiXk8zbKu1w --profiles`
- **Expected:** ERROR lines not attributed to a test listed, naming the test that was running (the last TEST-START).
- **Got:** "0 failing ... No test-level failure in this job", although the job is red with a `TEST-UNEXPECTED-FAIL` line.
- **Suggestion:** Attribute unattributed ERROR lines to the last TEST-START, as the profile's `output` markers already do; `intermittent --bug` could resolve "unknown test url" likewise.
- **Example from:** [intermittent/bugs/2056104.files/fx-tests-feedback.md](reports/intermittent/bugs/2056104.files/fx-tests-feedback.md)

## 116. `test` counts mochitest timeouts as FAIL when "Test timed out." is in `subtest` (4)

`fx-tests test` counted 8 harness timeouts as FAIL with "Failure details not recorded" and 0 timeouts, because the first TEST-UNEXPECTED-FAIL had an empty message and its text (`Test timed out.`) in the `subtest` field.

- **Question:** What failed in this test's 8 failing runs, and were they timeouts?
- **Command:** `fx-tests test dom/tests/mochitest/general/test_offsets.xhtml`
- **Expected:** 8 timeouts, Issue `Test timed out.`
- **Got:** `8 fail   0 timeout`; Issues `6x FAIL Failure details not recorded (likely Android or platform logging issue)` and `2x FAIL [SimpleTest.finish()] No checks actually run.`
- **Suggestion:** Take the message from `subtest` when `message` is empty, and classify a `Test timed out.` subtest as a timeout.
- **Example from:** [mac/dom/tests/mochitest/general/test_offsets.xhtml.files/fx-tests-feedback.md](reports/mac/dom/tests/mochitest/general/test_offsets.xhtml.files/fx-tests-feedback.md)

## 117. `task --messages` merges messages of the first run and the harness rerun (4)

`fx-tests task <id> --messages` prints one merged list per test. It doesn't say which leak came from the first run's TEST-FAIL and which from the rerun's TEST-UNEXPECTED-FAIL, and that split was the whole diagnosis.

- **Question:** Which failure messages came from the first run and which from the harness rerun?
- **Command:** `fx-tests task AKySeucETC2yR59OIaLDNw.0 --messages`
- **Expected:** Messages split per execution (initial run vs rerun).
- **Got:** One merged list: "1x leaked window until shutdown [url = about:newtab]", "1x ... [url = chrome://browser/content/browser.xhtml]", ...
- **Suggestion:** Group `--messages` by execution, labelled "initial" / "rerun", with the status (TEST-FAIL vs TEST-UNEXPECTED-FAIL).
- **Example from:** [intermittent/browser/components/aiwindow/ui/test/browser/browser_aiwindow_switcher.js.files/fx-tests-feedback.md](reports/intermittent/browser/components/aiwindow/ui/test/browser/browser_aiwindow_switcher.js.files/fx-tests-feedback.md)

## 118. No way to grep a text across a test's failing runs' logs (4)

`fx-tests errors --message` only searches typed markers, not the replayed plain stdout, so counting failing runs that printed a helper-process line found nothing and required downloading 442 MB of logs.

- **Question:** In how many of this test's 58 failing runs did the updater print `Failed to run application`?
- **Command:** `fx-tests errors --harness xpcshell --message "Failed to run application"`
- **Expected:** A count of failing runs whose output contains the line (answer was 4 of 58).
- **Got:** "No markers matched": the errors file only holds typed markers, not a child process's replayed plain stdout.
- **Suggestion:** `fx-tests test <path> --grep "<text>"` over failing runs' replayed output, counting matching runs.
- **Example from:** [mac/toolkit/mozapps/update/tests/unit_update_binary/marSuccessComplete.js.files/fx-tests-feedback.md](reports/mac/toolkit/mozapps/update/tests/unit_update_binary/marSuccessComplete.js.files/fx-tests-feedback.md)

## 119. `fx-tests test` covers trunk only without saying so, hiding esr/release perma-fails (4)

`fx-tests test <path>` gave a verdict of "intermittent, 99.83%" from trunk data, with no note on trees covered, while the test's bug was dominated by a perma-failure on mozilla-esr153/mozilla-release Windows.

- **Question:** What are all of this test's failures, on every tree?
- **Command:** `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_request_response_telemetry.js`
- **Expected:** The dominant failure (50 of 58 annotations, `Request timed out.` on esr153/release) shown, or a note that the verdict is trunk-only.
- **Got:** verdict "intermittent, 99.83%", Issues listing only trunk Win32 OOM/timeouts, and no hint that another tree fails it every run.
- **Suggestion:** Name the trees covered in the header; hint when the test's bugs have annotations mostly on other trees; consider `--tree`.
- **Example from:** [browser/browser/components/aiwindow/ui/test/browser/browser_smartwindow_request_response_telemetry.js.files/fx-tests-feedback.md](reports/browser/browser/components/aiwindow/ui/test/browser/browser_smartwindow_request_response_telemetry.js.files/fx-tests-feedback.md)

## 120. `intermittent --bug` lists crash fallout, not the test that crashes (4)

For a crash bug, `fx-tests intermittent --bug` "Tests named" listed `leakcheck`, `ShutdownLeaks` and an unrelated test. It never named the test where all 57 `JSAsyncTaskStarted` crashes happen.

- **Question:** Which tests and crash signature are behind this bug, and on which configs?
- **Command:** `fx-tests intermittent --bug 2036027`
- **Expected:** The bug's crash signature, the test it crashes in, and the unstarred occurrences of the same signature.
- **Got:** 3 macOS annotations only; "Tests named" lists `leakcheck`, `ShutdownLeaks` and another test, never `browser_toolbox_backward_forward_navigation.js`.
- **Suggestion:** Link the bug's assertion text or crash signature to the tests that crash with it.
- **Example from:** [intermittent/bugs/2036027.files/fx-tests-feedback.md](reports/intermittent/bugs/2036027.files/fx-tests-feedback.md)

## 121. `test --coverage` truncates to 10 rows and hides the skipped config (4)

`fx-tests test <path> --coverage` sorts by runs and cuts at 10 rows, so the one config where the test is skipped (0 runs) never shows. The summary line counts it without naming it, and there is no `--config` filter to find it.

- **Question:** On which config does the `skip-if` apply, so its removal can be verified there?
- **Command:** `fx-tests test devtools/client/netmonitor/test/browser_net_security-details.js --coverage`
- **Expected:** The one config where the test is skipped, named.
- **Got:** "linux 12/13 ran — 1 scheduled but skipped" without naming it; the skipped config is cut off ("… 16 more").
- **Suggestion:** Always print skipped-only configs or name them on the "scheduled but skipped" line; add a `--config <substring>` filter to `--coverage`.
- **Example from:** [devtools/devtools/client/netmonitor/test/browser_net_security-details.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/netmonitor/test/browser_net_security-details.js.files/fx-tests-feedback.md)

## 122. `intermittent --bug` can't list annotations before the index window (4)

The agent needed to know whether hangs predated a 2026-09-05 landing. `fx-tests intermittent --bug` only covers the index window, though Treeherder has the bug's older annotations.

- **Question:** Which jobs did sheriffs annotate on this bug before the index window?
- **Command:** `fx-tests intermittent --bug 1851682 --since 21 --limit 0`
- **Expected:** Annotated jobs going back before the window, to compare with a 2026-09-05 landing.
- **Got:** Only the window (2026-09-02 on).
- **Suggestion:** Let `intermittent --bug` reach older annotations, as Treeherder `api/failuresbybug/?startday=...` does.
- **Example from:** [intermittent/bugs/1851682.files/fx-tests-feedback.md](reports/intermittent/bugs/1851682.files/fx-tests-feedback.md)

## 123. `fx-tests failures --message` gives no per-config breakdown and refuses `--config` (4)

To check whether a message was macOS-only across 27 tests, the agent needed a config breakdown. `fx-tests failures --message` gives none and points nowhere, so it took one `fx-tests test --json` call per test plus a script.

- **Question:** Is `uncaught rejection: Connection is not open.` macOS-only across all 27 tests?
- **Command:** `fx-tests failures --harness xpcshell --message "Connection is not open" --tests --limit 0`
- **Expected:** A per-config (or per-platform) breakdown of the message's failures.
- **Got:** "This file records no job names, so nothing here can be broken down by configuration". Answer via 27 `test` calls: 528 of 528 on macOS.
- **Suggestion:** A `--configs` option on `failures --message` doing the per-test join, or a hint to use `fx-tests test --issue` per test.
- **Example from:** [mac/toolkit/components/nimbus/test/unit/shutdown/test_RSEL_updateRecipes_RemoteSettingsClient_get.js.connection-not-open.files/fx-tests-feedback.md](reports/mac/toolkit/components/nimbus/test/unit/shutdown/test_RSEL_updateRecipes_RemoteSettingsClient_get.js.connection-not-open.files/fx-tests-feedback.md)

## 124. `fx-tests task` puts end-of-manifest crash signatures under the wrong test (4)

`fx-tests task --messages` attributes minidumps processed at the end of a manifest to the Crash marker's test name. So a test that PASSed shows another test's `WriteLog` signature, and the test that actually crashed shows none.

- **Question:** Which test caused the `gfx::Log<1, CriticalLogger>::WriteLog` crash in this job?
- **Command:** `fx-tests task GQYf3gDHRlep__mE302Jmg --messages --full-messages`
- **Expected:** The `WriteLog` signature under `copy-texture-image-same-texture.html`, whose run printed the MOZ_CRASH and "Found unexpected crash dump file".
- **Got:** That test's row has no signature. `WriteLog` is listed under `texsubimage2d-4gb-wasm-memory.html` (which logged PASS) and, for the retry, under `tex-input-validation.html`.
- **Suggestion:** Join each `Crash` marker's Minidump ID to the test whose "Found unexpected crash dump file <id>.dmp" line names it.
- **Example from:** [mac/dom/canvas/test/webgl-conf/generated/test_2_conformance2__textures__misc__copy-texture-image-same-texture.html.files/fx-tests-feedback.md](reports/mac/dom/canvas/test/webgl-conf/generated/test_2_conformance2__textures__misc__copy-texture-image-same-texture.html.files/fx-tests-feedback.md)

## 125. `--durations` has no per-day breakdown; `--history` has no durations (4)

To find when a test's passing duration stepped up on a config, the agent looped `fx-tests test <path> --durations --day <d> --config a11y` over 7 days, since `--durations` gives one 21-day distribution and `--history` has no durations.

- **Question:** Did the passing duration on the a11y-checks configs move, and when?
- **Command:** `fx-tests test <path> --durations`
- **Expected:** Per-day median (and p95) duration next to the pass/fail counts, per config.
- **Got:** One distribution over the whole 21-day window; `--history` has pass/fail counts per day but no durations.
- **Suggestion:** Support `--history --durations` printing per-day median and p95 next to the pass/fail counts.
- **Example from:** [browser/browser/components/preferences/tests/home/browser_homepage_firefox_home_widgets.js.files/fx-tests-feedback.md](reports/browser/browser/components/preferences/tests/home/browser_homepage_firefox_home_widgets.js.files/fx-tests-feedback.md)

## 126. `test --task-ids` has no filter by message substring (3)

`fx-tests test --task-ids` can only be narrowed by `--issue`, which groups by first failure; here every run's first message is a TEST-KNOWN-FAIL, so two real failure modes share one issue and can't be separated.

- **Question:** Which jobs had failure mode X, and which messages co-occur with it?
- **Command:** `fx-tests test browser/components/backup/tests/browser/browser_settings.js --task-ids --limit 0 --config a11y-checks`
- **Expected:** A way to split the 69 failing jobs between `Node is not accessible via accessibility API` and `NotFoundError: Could not get children`.
- **Got:** A single issue row (109x) under `handleEvent() was unable to perform a11y checks on hidden node`; `--issue` cannot separate them.
- **Suggestion:** `--task-ids --message "<substring>"`, or skip TEST-KNOWN-FAIL/todo messages when picking the first failure.
- **Example from:** [browser/browser/components/backup/tests/browser/browser_settings.js.restore-a11y.files/fx-tests-feedback.md](reports/browser/browser/components/backup/tests/browser/browser_settings.js.restore-a11y.files/fx-tests-feedback.md)

## 127. `errors --message` queries one day at a time and refuses `--config` (3)

To find tests and configs carrying one log line, `fx-tests errors` had to be run per day (5 runs), then `fx-tests task` per task to get the config, because `--config` is refused.

- **Question:** Which other tests fail the same way (no message, same line in the replayed log)?
- **Command:** `fx-tests errors --harness xpcshell --day <D> --message "nsNotifyAddrListener released from static" --group-by test --task-ids`
- **Expected:** One command over the window giving the tests and configs carrying the line.
- **Got:** Per-day only, and `--config` refused ("the file records no job names").
- **Suggestion:** Add a multi-day query (`--since`/`--days`) to `errors`, and derive configs from the task IDs.
- **Example from:** [devtools/devtools/client/performance-new/test/xpcshell/test_webchannel-urls.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/performance-new/test/xpcshell/test_webchannel-urls.js.files/fx-tests-feedback.md)

## 128. `fx-tests failures --message` silently misses harness failures with no test path (3)

`fx-tests failures --message` returned "No failure matched" for RemoteProcessMonitor harness failures that have no test path, without saying such failures aren't in the data, so it read as "this never happens".

- **Question:** How often does "RemoteProcessMonitor | application timed out after 370 seconds" happen, and on which configs?
- **Command:** `COLUMNS=250 fx-tests failures --harness mochitest --message "timed out after 370 seconds" --tests`
- **Expected:** Rows for the RemoteProcessMonitor failures that `fx-tests intermittent --bug 1987687` lists (10 in 7 days).
- **Got:** "No failure matched. Searched 21,956 tests in mochitest-issues.json".
- **Suggestion:** Note that harness-level failures without a test path are not in this file.
- **Example from:** [intermittent/bugs/1987687.files/fx-tests-feedback.md](reports/intermittent/bugs/1987687.files/fx-tests-feedback.md)

## 129. `failures --tests` caps the per-row test list at 50 despite `--limit 0` (3)

`fx-tests failures --message ... --tests --limit 0` still capped the test list at 50 ("… 93 more tests"), and `--path` couldn't be combined with `--message` to narrow it to one directory.

- **Question:** Which tests in one manifest share a cascade failure message, in manifest order?
- **Command:** `fx-tests failures --harness mochitest --message 'waiting for vsync to be disabled' --tests --limit 0`
- **Expected:** All tests behind the message, or at least all in one directory (`--path` combined with `--message`).
- **Got:** The list is capped at 50 with "… 93 more tests (--json for all of them)" even with `--limit 0`.
- **Suggestion:** Honor `--limit 0` for the tests list too, not just rows.
- **Example from:** [browser/browser/components/extensions/test/browser/browser_ext_history_redirect.js.files/fx-tests-feedback.md](reports/browser/browser/components/extensions/test/browser/browser_ext_history_redirect.js.files/fx-tests-feedback.md)

## 130. `crash --raw` pid/uptime describe the macOS dump writer, not the hung child (3)

On macOS out-of-process dumps, `fx-tests crash --raw` gives the pid and `process_uptime` of the process that wrote the dump, not the hung child, with no warning. The agent chased a phantom "second child".

- **Question:** Which child process hung, for a `child process hang at shutdown` crash on macOS?
- **Command:** `fx-tests crash Mb0qnwpCTCCGHAkuohIC0Q.0 A156846A-F381-44C6-91F1-B58BFCF8F3B2 --raw`
- **Expected:** The hung child's pid and uptime, matching `Process 10925 hanging at shutdown` in the job's log.
- **Got:** pid 10903, uptime 14 s: both values belong to the dump writer.
- **Suggestion:** Note that pid/uptime are unreliable for mac out-of-process dumps, or take the hung child's pid from the `Process N hanging at shutdown` log line.
- **Example from:** [devtools/devtools/server/tests/xpcshell/test_safe-getter.js.files/fx-tests-feedback.md](reports/devtools/devtools/server/tests/xpcshell/test_safe-getter.js.files/fx-tests-feedback.md)

## 131. `--config` prefix match silently merges `-nofis`/`-cf` sibling configs (3)

`--config` matches by prefix, so `--config ...-ccov/opt-xpcshell` also counts `...-nofis`, summing two configs with no note of which matched.

- **Question:** What is the failure count and parallel rate on the fission ccov xpcshell config alone?
- **Command:** `fx-tests test <path> --executions --config test-linux2404-64-ccov/opt-xpcshell`
- **Expected:** 56 failures, that config only.
- **Got:** 117 failures, 133 parallel runs: the sum of `ccov/opt-xpcshell` and `ccov/opt-xpcshell-nofis`.
- **Suggestion:** Match configs exactly, or print the matched config names under the filter.
- **Example from:** [browser/browser/components/shell/test/unit/test_dynamicLauncher.js.ccov-timeout.files/fx-tests-feedback.md](reports/browser/browser/components/shell/test/unit/test_dynamicLauncher.js.ccov-timeout.files/fx-tests-feedback.md)

## 132. `skips` reports stale conditions and drops manifest `# Bug` comments (3)

`fx-tests skips --path devtools/` returned conditions seen over the window without their `# Bug NNN` comments, and without flagging those already removed or turned into `run-if` on main. `disabled` came back as a condition.

- **Question:** Which of a test's skip conditions are still on main, and which bug is next to each?
- **Command:** `fx-tests skips --path devtools/ --limit 0 --json`
- **Expected:** The manifest's current `skip-if` lines with their `# Bug NNN` comments.
- **Got:** Conditions without comments; 62 of 554 rows no longer have any `skip-if` on main; `disabled = "Bug 962258"` comes back as condition `Bug 962258`.
- **Suggestion:** Flag each condition as `removed since` / `now run-if`, show manifest comments, and expose `disabled` as its own field.
- **Example from:** [devtools/todo.files/fx-tests-feedback.md](reports/devtools/todo.files/fx-tests-feedback.md)

## 133. `test` Issues ranks a shutdown leak as a run's first failure (3)

`fx-tests test <path>` Issues (first failure per run) put `leaked window until shutdown` first in 18 of 20 runs. The harness stamps a leak with the window's creation time, so the leak hid the exception that caused it.

- **Question:** What is this test's real failure mode?
- **Command:** `fx-tests test browser/components/aiwindow/ui/test/browser/browser_security_run_search.js`
- **Expected:** One failure mode, `Uncaught exception in test - [MockEngineManager] Failed to find a request …`, in all 20 failing runs.
- **Got:** `18x leaked window until shutdown [url = chrome://browser/content/browser.xhtml]` and `2x … MockEngineManager …`.
- **Suggestion:** Skip shutdown leak messages when a run has another failure, or mark them as logged at shutdown.
- **Example from:** [browser/browser/components/aiwindow/ui/test/browser/browser_security_run_search.js.files/fx-tests-feedback.md](reports/browser/browser/components/aiwindow/ui/test/browser/browser_security_run_search.js.files/fx-tests-feedback.md)

## 134. `intermittent --bug` doesn't hint that jobs are now starred on other bugs (3)

`fx-tests intermittent --bug 2056657` showed no annotations after 2026-09-08, which reads as fixed. The same jobs were now starred on other bugs because a harness change altered the failure line; nothing in the output hinted at that.

- **Question:** Is the bug still failing, or did only its message change?
- **Command:** `fx-tests intermittent --bug 2056657`
- **Expected:** For the bug's job names, the bugs their failures were starred on after the last occurrence.
- **Got:** No annotations after 2026-09-08, with no hint the jobs are now starred on 2070794, 2071660, 2070405.
- **Suggestion:** List the bugs later failures of the same job names were starred on.
- **Example from:** [intermittent/bugs/2056657.files/fx-tests-feedback.md](reports/intermittent/bugs/2056657.files/fx-tests-feedback.md)

## 135. `crash --all-threads` flags idle event-loop threads blocked, misses stuck thread (3)

The "Waiting on a lock" hint of `fx-tests crash --all-threads` listed 12 ordinary idle event-loop threads (condvar waits). It did not flag the actual stuck `AudioIPC Server` thread.

- **Question:** Which thread is the hang's culprit?
- **Command:** `fx-tests crash W7i92NMQQqW_tNRJJuib5g 1545fdfc-f2de-0b7c-e2e9-2207439e2c40 --all-threads`
- **Expected:** The hang's culprit highlighted.
- **Got:** `Waiting on a lock: #15, #23, #25, ...`: 12 idle event loops; #58 `AudioIPC Server` spinning in `wait_for_state_change` was not flagged.
- **Suggestion:** Treat condvar waits under `ThreadEventQueue::GetEvent` / `nsAppShell::Queue::Pop` / `event_base_loop` as idle; flag threads inside `Drop`/destroy or `wait_for_*` helpers.
- **Example from:** [intermittent/dom/worklet/tests/test_audioWorklet_WASM.html.files/fx-tests-feedback.md](reports/intermittent/dom/worklet/tests/test_audioWorklet_WASM.html.files/fx-tests-feedback.md)

## 136. `fx-tests task` doesn't retry a transient task-definition read, header loses revision (3)

`fx-tests task` failed to read the task definition once and printed the header without job name, repository or revision. A `curl` of the same definition a minute later worked, so it was transient.

- **Question:** What revision did this failing task run on?
- **Command:** `fx-tests task XzvJ4VKtSuGqLmkt8QN8fA --profiles`
- **Expected:** A header with job name, repository and revision.
- **Got:** "warning: could not read the definition of task XzvJ4VKtSuGqLmkt8QN8fA, so the job name, repository and revision are missing from the header".
- **Suggestion:** Retry the task-definition read inside fx-tests.
- **Example from:** [mac/devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-sidebar-toggle.js.files/fx-tests-feedback.md](reports/mac/devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-sidebar-toggle.js.files/fx-tests-feedback.md)

## 137. Cached data window moves between `fx-tests test` calls without notice (3)

During one session, a newer data file replaced the cached one, so `fx-tests test` moved from 2026-08-31..09-20 to 2026-09-01..09-21. Counts changed (45/9,939 to 42/10,334) and nothing flagged it.

- **Question:** Do counts from an earlier `fx-tests test` call still match later calls in the same session?
- **Command:** `fx-tests test <path>`
- **Expected:** Consistent counts across calls, or a notice that the window moved.
- **Got:** First call covered 2026-08-31..2026-09-20 (45 fails / 9,939 runs); later calls 2026-09-01..2026-09-21 (42 / 10,334). Only the "generated" line shows it.
- **Suggestion:** Print a one-line stderr notice when the cached data file is replaced between calls.
- **Example from:** [mac/browser/components/extensions/test/browser/browser_ext_identity_indication.js.files/fx-tests-feedback.md](reports/mac/browser/components/extensions/test/browser/browser_ext_identity_indication.js.files/fx-tests-feedback.md)

## 138. `crashes --signature` gives no per-config breakdown and caps tests at 50 (2)

`fx-tests crashes --signature` could not break a signature (19,349 crashes, 503 tests) down by configuration, and its JSON tests list stops at 50. The agent looped `fx-tests test` 100 times to aggregate configs.

- **Question:** On which configs does this crash signature happen, across all its tests?
- **Command:** `fx-tests crashes --signature "child process hang at shutdown"`
- **Expected:** A per-config breakdown for the signature (19,349 crashes, 503 tests).
- **Got:** "This file records no job names, so nothing here can be broken down by configuration", and the JSON `tests` array stops at 50 entries.
- **Suggestion:** Add `crashes --signature X --by-config` doing the per-test fan-out, and don't cap the tests list.
- **Example from:** [intermittent/bugs/no-bug.macos1015-child-process-hang-at-shutdown.files/fx-tests-feedback.md](reports/intermittent/bugs/no-bug.macos1015-child-process-hang-at-shutdown.files/fx-tests-feedback.md)

## 139. `errors --config` refused: "the file records no job names" (2)

`fx-tests errors --config` is refused because the errors file records no job names, so comparing an error's rate between ASan and debug needed a script fetching each task's name from Taskcluster.

- **Question:** On which configurations did this message appear on that day?
- **Command:** `fx-tests errors --file process_watcher_win --config asan`
- **Expected:** Rows restricted to ASan jobs, to compare the `CreateRemoteThread: error 5` rate on ASan against debug.
- **Got:** `--config cannot be applied to mochitest-<date>-errors.json: the file records no job names`.
- **Example from:** [intermittent/bugs/1944623.files/fx-tests-feedback.md](reports/intermittent/bugs/1944623.files/fx-tests-feedback.md)

## 140. `try --messages` doesn't say which configs/tasks produced each message (2)

`fx-tests try --task-ids --messages` gives message counts for a whole row. Which jobs produced the rarer message (the key clue: it split by config) took one `fx-tests task <id> --messages` per task (8 calls).

- **Question:** Which jobs produced the less frequent message (`Fullscreen request should be canceled.`, 8x)?
- **Command:** `fx-tests try <rev> --task-ids --messages`
- **Expected:** Each message attributed to the configs or tasks that produced it.
- **Got:** Message counts for the whole row, with no per-config or per-task attribution.
- **Suggestion:** Show per-message config counts, e.g. `8x Fullscreen request should be canceled. (debug-mochitest-plain-2 ×4, opt-mochitest-plain-4 ×4)`.
- **Example from:** [mac/dom/base/test/test_lock_orientation_with_pending_fullscreen.html.files/fx-tests-feedback.md](reports/mac/dom/base/test/test_lock_orientation_with_pending_fullscreen.html.files/fx-tests-feedback.md)

## 141. `fx-tests test` verdict says intermittent when all failures come from one push (2)

`fx-tests test` called a test intermittent although all 6 crashes came from one autoland revision, a backed-out regression. `--task-ids` shows no revision, so the agent ran `fx-tests task` on each job.

- **Question:** Did all of these failures come from one push?
- **Command:** `fx-tests test toolkit/content/tests/widgets/test_videocontrols_focus.html`
- **Expected:** The verdict to say all 6 crashes came from a single revision (autoland 0a3b36a2cebf).
- **Got:** "Verdict: intermittent. Fails on 3 configurations; worst ... 0.4%".
- **Suggestion:** A revision column in `--task-ids`, and a verdict like "all failures from 1 push (0a3b36a2cebf): likely a regression".
- **Example from:** [mac/toolkit/content/tests/widgets/test_videocontrols_focus.html.files/fx-tests-feedback.md](reports/mac/toolkit/content/tests/widgets/test_videocontrols_focus.html.files/fx-tests-feedback.md)

## 142. `intermittent --bug` shows messages, trees, platforms as separate histograms, not cross-tabulated (2)

`fx-tests intermittent --bug` listed failure messages, trees and platforms as independent histograms, so tying each message to the trees it occurs on (52 of 60 on esr153/release) needed a script over `--json`.

- **Question:** On which trees and versions does each failure message of a bug occur?
- **Command:** `fx-tests intermittent --bug 2030788 --since 21 --tree all --limit 0`
- **Expected:** The "Failure messages" block broken down by tree/platform.
- **Got:** Messages, trees and platforms as three independent histograms; tying message to tree needed a script over `--json` `occurrenceRows[].lines`.
- **Suggestion:** Show a message x tree (or platform/build type) table, and the app version per occurrence.
- **Example from:** [intermittent/browser/components/aiwindow/ui/test/browser/browser_smartwindow_request_response_telemetry.js.files/fx-tests-feedback.md](reports/intermittent/browser/components/aiwindow/ui/test/browser/browser_smartwindow_request_response_telemetry.js.files/fx-tests-feedback.md)

## 143. No flag for a content-process crash without minidump; TIMEOUT only (2)

When no minidump is written (ASan, crash reporter off), `fx-tests task` and `test` show just `TIMEOUT` with `0 crash`. The "remote browser crashed while on" output inside the failing test's span is never surfaced.

- **Question:** Was there a content crash during this failing test?
- **Command:** `fx-tests task IjDP8JAqR1uwmIEIlgccCw --profiles`
- **Expected:** Some hint that the TIMEOUT coincided with a content process crash ("remote browser crashed while on" inside the test's span).
- **Got:** `TIMEOUT` / `Test timed out` only; `0 crash`, since no minidump was written.
- **Suggestion:** Surface "remote browser crashed" output inside a failing test's span as a per-failure flag.
- **Example from:** [devtools/devtools/client/debugger/test/mochitest/browser_dbg-inline-script-offset.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/debugger/test/mochitest/browser_dbg-inline-script-offset.js.files/fx-tests-feedback.md)

## 144. `test --durations` truncation hides the failing config's row (2)

`fx-tests test <path> --durations` lists the 10 slowest configs and truncates the rest, so the only failing config's duration row was hidden; `--limit 0 | grep` was needed.

- **Question:** How long does a passing run take on the one failing config (opt-xpcshell-msix)?
- **Command:** `fx-tests test devtools/server/tests/xpcshell/test_connection_closes_all_pools.js --durations`
- **Expected:** That config's row, since it is the only failing config and the verdict names it.
- **Got:** The 10 slowest configs (ccov, tsan, debug), `… 29 more (--limit 0 for all)`; the msix rows were not among them.
- **Suggestion:** Always show the failing configs' rows, or accept `--config` with `--durations`.
- **Example from:** [devtools/devtools/server/tests/xpcshell/test_connection_closes_all_pools.js.files/fx-tests-feedback.md](reports/devtools/devtools/server/tests/xpcshell/test_connection_closes_all_pools.js.files/fx-tests-feedback.md)

## 145. `errors --message` undercounts occurrences present in job logs (2)

`fx-tests errors --message` found 1-2 occurrences per day of a fatal C++ assertion, while 11 of 20 downloaded job logs from the same days contained it. The output doesn't say how many jobs it did not cover.

- **Question:** How many xpcshell jobs a day log `ASSERTION: Overwriting an existing document channel!`?
- **Command:** `fx-tests errors --harness xpcshell --message "Overwriting an existing document channel"`
- **Expected:** Roughly one occurrence per job that crashed this way.
- **Got:** 1 occurrence on 2026-09-21 and 2 on 2026-09-20; yet 11 of 20 downloaded job logs from those days contain the assertion.
- **Suggestion:** Say how many of the day's jobs were not covered by the errors data.
- **Example from:** [intermittent/bugs/2043133.files/fx-tests-feedback.md](reports/intermittent/bugs/2043133.files/fx-tests-feedback.md)

## 146. `errors --harness` accepts only xpcshell or mochitest (2)

`fx-tests errors` only covers mochitest and xpcshell. Asking which reftest/crashtest jobs hit a terminator shutdown hang, where 8 of 9 annotated failures were, meant grepping each job's log.

- **Question:** Which jobs hit this non-test crash (terminator shutdown hang) on reftest/crashtest?
- **Command:** `fx-tests errors --message "CreateRemoteThread"`
- **Expected:** A way to ask the same of reftest/crashtest jobs.
- **Got:** Mochitest only; `--harness` accepts only xpcshell|mochitest.
- **Example from:** [intermittent/bugs/1944623.files/fx-tests-feedback.md](reports/intermittent/bugs/1944623.files/fx-tests-feedback.md)

## 147. `crash` with a short minidump ID says the artifact expired (2)

Reports quote the first 8 hex digits of a minidump ID. `fx-tests crash <task> 8ad80c47` doesn't match the prefix and blames artifact expiry, though the task is 8 days old and its other dumps can still be fetched.

- **Question:** Which minidump is the hung parent process, and what was its main thread doing?
- **Command:** `fx-tests crash O2OKFBENRJ-Y0bH32depyA 8ad80c47 --thread 0 --frames 0`
- **Expected:** A match on the ID prefix, or "no minidump with that ID; pass the full UUID".
- **Got:** exit 4, `the artifact is not there. Taskcluster expires artifacts, so a dump from an old task is permanently gone — retrying will not help.`
- **Suggestion:** Resolve unique ID prefixes in `crash` and `task` (the latter answers HTTP 400 to a task-ID prefix).
- **Example from:** [browser/browser/components/tests/browser/browser_last_window_close_trigger.js.files/fx-tests-feedback.md](reports/browser/browser/components/tests/browser/browser_last_window_close_trigger.js.files/fx-tests-feedback.md)

## 148. `test --profiles --task-ids --issue N`: Profiles block ignores `--issue` (2)

With `--issue 9`, `fx-tests test` filtered the Task IDs block to that failure mode. The Profiles block still listed resource-usage URLs for jobs of other failure modes.

- **Question:** Where are the profiles of this failure mode's jobs?
- **Command:** `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_contentscript_triggeringPrincipal.js --profiles --task-ids --issue 9 --limit 0`
- **Expected:** Profile URLs for the 59 jobs of issue 9 (`CRASH @ NS_DispatchToMainThread`).
- **Got:** Profiles listed jobs of other modes (Android and linux-artifact timeouts), and none of the issue-9 jobs checked.
- **Suggestion:** Make the Profiles block honour `--issue`.
- **Example from:** [intermittent/toolkit/components/extensions/test/xpcshell/test_ext_contentscript_triggeringPrincipal.js.imageloader-shutdown-crash.files/fx-tests-feedback.md](reports/intermittent/toolkit/components/extensions/test/xpcshell/test_ext_contentscript_triggeringPrincipal.js.imageloader-shutdown-crash.files/fx-tests-feedback.md)

## 149. `test --task-ids` and `intermittent` don't show sheriff "fixed by commit" classifications (2)

All failures came from one autoland push, with 3 identical failing runs, 2 classified "fixed by commit". `fx-tests test` and `fx-tests intermittent` showed one task and one annotation, so a regression from a backed-out patch read as a rare intermittent.

- **Question:** Were these failures caused by a push that was later backed out?
- **Command:** `fx-tests test devtools/client/inspector/rules/test/browser_rules_original-source-link.js --history --task-ids --limit 0`
- **Expected:** All failures on one revision (b606af2b1dd6), 3 failing runs of one job, 2 classified "fixed by commit".
- **Got:** One task (NPZ-lvP8TBu_YrCw7jYOMQ.0) and "1 sheriff annotation" on the intermittent bug; nothing about the other runs or classifications.
- **Suggestion:** Show sheriff classifications of failing jobs (e.g. "fixed by commit") next to their task IDs.
- **Example from:** [devtools/devtools/client/inspector/rules/test/browser_rules_original-source-link.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/inspector/rules/test/browser_rules_original-source-link.js.files/fx-tests-feedback.md)

## 150. `fx-tests test --config` replaces the failure message with "Failure details not recorded" (2)

With `--config`, `fx-tests test` Issues attribute the same failures differently. 10 Mac failures listed under a TypeError without the filter become "Failure details not recorded" with `--config macosx1500`.

- **Question:** What are the failure modes of this test on Mac?
- **Command:** `fx-tests test <path> --config macosx1500`
- **Expected:** The same Issues rows as without the filter, restricted to Mac: `TypeError: can't access property "textContent", row.renderRoot.querySelector(...) is null`.
- **Got:** `10x FAIL Failure details not recorded (likely Android or platform logging issue)` for those same 10 Mac failures.
- **Suggestion:** Keep the same Issues attribution with and without `--config`.
- **Example from:** [mac/browser/components/aiwindow/ui/test/browser/browser_smartwindow_smartformfill_autocomplete.js.files/fx-tests-feedback.md](reports/mac/browser/components/aiwindow/ui/test/browser/browser_smartwindow_smartformfill_autocomplete.js.files/fx-tests-feedback.md)

## 151. `test --task-ids --issue N` and `task --messages` disagree on a task's failure (2)

`fx-tests test ... --task-ids --issue 2` filed a task under "uncaught rejection: profileList.value.map is not a function", but `fx-tests task` for that task didn't show that message. The reverse happened for another task.

- **Question:** Which failure mode did this task have?
- **Command:** `fx-tests task dIgh7VEYQDO8Su2EyF7zkA --profiles --messages`
- **Expected:** The issue 2 message "uncaught rejection: profileList.value.map is not a function" (and the `profilesSelect is null` failure) in the task's messages.
- **Got:** Only 3 messages: `changed preference: identity.fxaccounts.account.device.name`, `Found an unexpected tab at the end of test run: about:blank`, `The profiles note is hidden`.
- **Example from:** [browser/browser/components/profiles/tests/browser/browser_preferences.js.files/fx-tests-feedback.md](reports/browser/browser/components/profiles/tests/browser/browser_preferences.js.files/fx-tests-feedback.md)

## 152. Usage error for `--since <date>` exits with status 0 (2)

`fx-tests test ... --since 2026-09-21` printed a usage error but exited 0, so a pipe hid it; the guide says usage errors exit 1.

- **Question:** Which failing task ids are there since a given date?
- **Command:** `fx-tests test <path> --task-ids --limit 0 --since 2026-09-21`
- **Expected:** Task ids since that date, or a usage error with exit status 1.
- **Got:** `--since expects a non-negative integer, got "2026-09-21"`, exit 0 (so a pipe hid it).
- **Suggestion:** Exit with status 1 on usage errors.
- **Example from:** [browser/browser/components/translations/tests/browser/browser_translations_full_page_intersection_engine_timeout.js.files/fx-tests-feedback.md](reports/browser/browser/components/translations/tests/browser/browser_translations_full_page_intersection_engine_timeout.js.files/fx-tests-feedback.md)

## 153. `intermittent --bug` occurrences lack duration, maxRunTime and how far the job got (2)

For a max-run-time meta bug, `fx-tests intermittent --bug` lists only job names, platforms and task ids, so 163 heterogeneous jobs couldn't be grouped by what killed them without parsing every log.

- **Question:** Which mechanisms are behind this meta bug's annotated jobs?
- **Command:** `fx-tests intermittent --bug 1809667 --limit 0`
- **Expected:** Some way to group 163 heterogeneous jobs by what killed them.
- **Got:** Job names, platforms, and task ids — nothing about duration, the task's maxRunTime, or how far it got.
- **Suggestion:** Show per occurrence its maxRunTime, the job's typical passing duration, setup time, and the last `TEST-START`.
- **Example from:** [intermittent/bugs/1809667.files/fx-tests-feedback.md](reports/intermittent/bugs/1809667.files/fx-tests-feedback.md)

## 154. `fx-tests intermittent --bug` doesn't warn when annotations name other tests (2)

`fx-tests intermittent --bug` showed annotations whose failure lines were all an unrelated Android reftest failure, with no warning that none name the bug's test; a reader could take them for its rate.

- **Question:** Are the jobs starred on this bug really the bug's test?
- **Command:** `fx-tests intermittent --bug 2057939`
- **Expected:** A warning that none of the annotated jobs' failure lines name the bug's test (`test_proxy.py::test_mitm`).
- **Got:** 5 annotations, all Android `geckoview-reftest` "incomplete after application is no longer top", shown without comment.
- **Suggestion:** Warn when the annotated jobs' failure lines don't name the bug's test.
- **Example from:** [intermittent/testing/mozbase/mozproxy/tests/test_proxy.py.files/fx-tests-feedback.md](reports/intermittent/testing/mozbase/mozproxy/tests/test_proxy.py.files/fx-tests-feedback.md)

## 155. `intermittent --bug` doesn't show the same failure starred on other bugs (2)

`fx-tests intermittent --bug 1924784` gave no hint that the same ScriptTimeout failure was also starred on bug 2052509 in the same week, so each bug alone undercounted it by a third to two thirds.

- **Question:** Is the same failure also starred on another bug?
- **Command:** `fx-tests intermittent --bug 1924784`
- **Expected:** The other bugs whose annotated jobs fail with the same message.
- **Got:** Only this bug's annotations; the same `test_backup_disablement_in_new_session` ScriptTimeout starred on bug 2052509 (12 jobs) was not mentioned.
- **Suggestion:** List other bugs where the same failure message or job type is starred in the window.
- **Example from:** [intermittent/browser/components/backup/tests/marionette/test_backup.py.bug1924784.files/fx-tests-feedback.md](reports/intermittent/browser/components/backup/tests/marionette/test_backup.py.bug1924784.files/fx-tests-feedback.md)

## 156. `test` has no jit-test data and doesn't say the harness is unsupported (2)

`fx-tests test` on a jit-test says the path isn't in the data and suggests it was renamed, added later or never run in CI. None of that was true: jit-test just isn't covered. `task` on a jittest job only prints a missing-profile error.

- **Question:** Is this jit-test still failing, since when, and on which worker image?
- **Command:** `fx-tests test js/src/jit-test/tests/strings/bug1972669.js`
- **Expected:** Pass/fail rates and history for a jit-test, or a message saying jit-test isn't indexed.
- **Got:** `No test path in the xpcshell and mochitest 21-day data contains ...`, followed by "renamed / added after the window / never run in CI".
- **Suggestion:** Say explicitly that the jit-test harness is unsupported; ideally add jit-test support, and have `task` list outcomes from the log.
- **Example from:** [intermittent/js/src/jit-test/tests/strings/bug1972669.js.files/fx-tests-feedback.md](reports/intermittent/js/src/jit-test/tests/strings/bug1972669.js.files/fx-tests-feedback.md)

## 157. "Fails almost only in parallel" verdict contradicts its own rates (2)

`fx-tests test` headlines "Fails almost only in parallel" while its own numbers show a sequential failure rate 8x higher, presumably because the sequential population is mostly harness reruns of failures.

- **Question:** Does this test fail only when run in parallel?
- **Command:** `fx-tests test netwerk/test/unit/test_cookies_purge_counting_per_host.js`
- **Expected:** "Fails almost only in parallel" backed by a higher parallel failure rate.
- **Got:** `Fails almost only in parallel: 47 of 48 mode-recorded failures, 0.4% of parallel runs against 3.2% of sequential ones`.
- **Suggestion:** Say what the sequential population is (reruns of failed tests?), or drop the rate comparison when that biased.
- **Example from:** [intermittent/netwerk/test/unit/test_cookies_purge_counting_per_host.js.files/fx-tests-feedback.md](reports/intermittent/netwerk/test/unit/test_cookies_purge_counting_per_host.js.files/fx-tests-feedback.md)

## 158. `fx-tests issues` has no `--harness all` for one cross-harness list (2)

Building a folder's list of failing and skipped tests needed four queries (`issues` and `skips`, per harness) joined by script. `issues --group-by test` rows have a `skipCount` but not the skip conditions.

- **Question:** What are the failing and skipped tests of a folder, both harnesses, in one ranked list?
- **Command:** `fx-tests issues --harness mochitest|xpcshell --path devtools/ --type fail --type timeout --type crash --group-by test --limit 0 --json`
- **Expected:** One list across harnesses, each failing test carrying its skip conditions when it is also skipped.
- **Got:** four separate lists; merging and joining them took a script over `--json`.
- **Suggestion:** Add `issues --harness all`, and skip conditions on `--group-by test` rows with `skipCount > 0`.
- **Example from:** [devtools/todo.files/fx-tests-feedback.md](reports/devtools/todo.files/fx-tests-feedback.md)

## 159. `issues --group-by test` lacks skip conditions; no `--harness all` (2)

Building one ranked list of a folder's failing and skipped tests took four queries (`issues` and `skips`, per harness) joined by a script. `issues` rows show `skipCount` but not the conditions, and `skips` doesn't say if the test also fails.

- **Question:** What are the failing and skipped tests of a folder, both harnesses, in one ranked list?
- **Command:** `fx-tests issues --harness mochitest|xpcshell --path devtools/ --type fail --type timeout --type crash --group-by test --limit 0 --json`
- **Expected:** One list across harnesses, each failing test carrying its skip conditions when it is also skipped.
- **Got:** Four separate lists; `issues` rows have a `skipCount` but not the conditions, and `skips` doesn't say whether the test also fails.
- **Suggestion:** Add `issues --harness all`, and put the skip conditions on `issues --group-by test` rows with `skipCount > 0`.
- **Example from:** [devtools/todo.files/fx-tests-feedback.md](reports/devtools/todo.files/fx-tests-feedback.md)

## 160. `issues` doesn't flag tests deleted from main during the window (2)

`fx-tests issues --path browser/` returned 5 rows for tests deleted on main during the window (e.g. `browser_aiwindow_group_tabs_button.js`, split into four files), undistinguished from live ones. Finding them meant checking every path against the manifests on `origin/main`.

- **Question:** Which failing tests no longer exist on main?
- **Command:** `fx-tests issues --path browser/ ...`
- **Expected:** A "last seen" date per test, or a flag when the test is absent from the latest day's manifests.
- **Got:** 5 rows for deleted tests, undistinguished from live ones.
- **Suggestion:** Flag paths absent from the latest manifests with a "last seen" date; in `intermittent`, give the new path of moved tests.
- **Example from:** [browser/todo.files/fx-tests-feedback.md](reports/browser/todo.files/fx-tests-feedback.md)

## 161. `fx-tests intermittent` has no `--path` to list a folder's annotated bugs (2)

To list the annotated bugs of `devtools/`, the agent found that `fx-tests intermittent` has no `--path` filter, unlike `issues` and `skips`. It had to filter the rows by script.

- **Question:** Which annotated bugs belong to this folder?
- **Command:** `fx-tests intermittent --harness mochitest --since 21 --limit 0 --json`
- **Expected:** `--path devtools/`, as `issues` and `skips` take.
- **Got:** No `--path`; filtered the 455 mochitest and 60 xpcshell rows to 25 devtools ones by script.
- **Suggestion:** Add `--path` to `intermittent`.
- **Example from:** [devtools/todo.files/fx-tests-feedback.md](reports/devtools/todo.files/fx-tests-feedback.md)

## 162. `fx-tests test` drops runs with `test_start` but no `test_end`, showing 0 timeouts (2)

`fx-tests test` reported 0 timeouts for test_crash_phc.js, although it timed out in 6 jobs. The harness raised before logging `test_end`, so only the retry's PASS was recorded.

- **Question:** How often does this test time out, when the harness never logged the TIMEOUT?
- **Command:** `fx-tests test toolkit/crashreporter/test/unit/test_crash_phc.js`
- **Expected:** The timeouts, or a warning that some runs have no result.
- **Got:** "0 timeout", while the jobs carry an ERROR log `test_start for <test> logged while in progress.`
- **Suggestion:** Count a run with a `test_start` and no `test_end` as "no result" instead of dropping it.
- **Example from:** [intermittent/bugs/2058686.files/fx-tests-feedback.md](reports/intermittent/bugs/2058686.files/fx-tests-feedback.md)

## 163. `fx-tests test` has no per-repo (mozilla-central vs autoland) split (2)

A shippable config failed only on mozilla-central (update channel `nightly` vs `nightly-autoland`), but `fx-tests test --task-ids` groups task IDs by day only. The agent ran `fx-tests task` per task to see each repo.

- **Question:** Which repository (mozilla-central vs autoland) are the failures from?
- **Command:** `fx-tests test browser/components/contextualidentity/test/browser/browser_switchTab_across_user_context.js --task-ids --limit 0`
- **Expected:** A repo column or a per-repo split of the failures.
- **Got:** Task IDs grouped by day only.
- **Suggestion:** Add a `repo` column (fails/runs per repo) to the Failing configurations table, or to `--task-ids`.
- **Example from:** [browser/browser/components/contextualidentity/test/browser/browser_switchTab_across_user_context.js.files/fx-tests-feedback.md](reports/browser/browser/components/contextualidentity/test/browser/browser_switchTab_across_user_context.js.files/fx-tests-feedback.md)

## 164. `try --all-jobs --task-ids` gives no task IDs for passing runs (2)

`fx-tests try` showed `2/9` for a test with task IDs only for the failing jobs. No passing task ID appears anywhere, even in `--json`, so a passing run's profile couldn't be compared with the failing ones.

- **Question:** On which jobs of this try push did this test pass, and with which task IDs?
- **Command:** `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --task-ids`
- **Expected:** The jobs where the test ran and passed, with task IDs.
- **Got:** `2/9` in the table, and task IDs only for the 2 failing jobs; the breakdown is only in `--json` `outcomes`, with no passing task IDs.
- **Suggestion:** List passing jobs and task IDs under the row (at least with `--all-jobs`) and in JSON.
- **Example from:** [devtools/devtools/client/aboutdebugging/test/browser/browser_aboutdebugging_addons_debug_storage.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/aboutdebugging/test/browser/browser_aboutdebugging_addons_debug_storage.js.files/fx-tests-feedback.md)

## 165. TEST-UNEXPECTED-PASS (fail-if) failure shown with no message in `task` (2)

`fx-tests task <id> --messages` lists a fail-if test that passed as a failure with no message, so the failure mode is invisible; `fx-tests try --messages` behaves the same.

- **Question:** What did this test fail with in this job?
- **Command:** `fx-tests task H6RcZP1URlSmjnjo-6UreA --messages --full-messages`
- **Expected:** `We expect at least one assertion to fail because this test file is marked as fail-if in the manifest.`, or "unexpected pass (fail-if)".
- **Got:** `FAIL — 2 failing executions of 2` and no message at all.
- **Suggestion:** Record the TEST-UNEXPECTED-PASS message, or label the failure "unexpected pass (fail-if)".
- **Example from:** [devtools/devtools/client/inspector/animation/test/browser_animation_animation-target_highlight.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/inspector/animation/test/browser_animation_animation-target_highlight.js.files/fx-tests-feedback.md)

## 166. `try` rows don't count jobs per config, so 1-of-3 reads as perma-fail (2)

A `fx-tests try --all-jobs` NEW INTERMITTENTS row names the failing config but not how many jobs of it ran the test. The 2 passes were in other jobs of the same config, found only by grepping `--json` and running `task` on each.

- **Question:** On which jobs/configs did the passing runs of this newly-failing test run?
- **Command:** `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs`
- **Expected:** A line naming the jobs of the 2 passing runs for the `2/4` row.
- **Got:** The row says `2/4`; details name only the failing job. `--json` only gives counts (`totalRuns: 4, totalJobs: 3, outcomes.passed: 2`).
- **Suggestion:** Add a "passed on: <job names>" line, and a per-config count of jobs that ran the test.
- **Example from:** [devtools/devtools/client/netmonitor/test/new-resend-request/browser_net_new_request_panel_context_menu.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/netmonitor/test/new-resend-request/browser_net_new_request_panel_context_menu.js.files/fx-tests-feedback.md)

## 167. `fx-tests test` Issues shows "Test exceeded time limit" instead of logged timeout message (2)

`fx-tests test` Issues labels an "application timed out after 370.0 seconds with no output" failure as `Test exceeded time limit`, hiding that the harness killed the whole browser; the real message only shows in `fx-tests task`.

- **Question:** What kind of timeout is this test's failure?
- **Command:** `fx-tests test gfx/layers/apz/test/mochitest/test_group_zoom-2.html`
- **Expected:** The verbatim message, saying the harness killed the whole browser rather than a JS-harness test timeout.
- **Got:** `TIMEOUT Test exceeded time limit`; the real message only shows in `fx-tests task`.
- **Suggestion:** Show the verbatim logged message in Issues.
- **Example from:** [intermittent/gfx/layers/apz/test/mochitest/test_group_zoom-2.html.files/fx-tests-feedback.md](reports/intermittent/gfx/layers/apz/test/mochitest/test_group_zoom-2.html.files/fx-tests-feedback.md)

## 168. `intermittent --config` matches platform, not job name as `--help` says (2)

`fx-tests intermittent --bug 1809667 --config talos` returned "none match the filter" although 26 occurrences were Talos jobs; `--help` says `--config` takes job-name substrings, but it apparently matches the platform.

- **Question:** Which of a meta bug's annotations are Talos jobs?
- **Command:** `fx-tests intermittent --bug 1809667 --limit 0 --config talos --json`
- **Expected:** The 26 occurrences whose job (`testSuite`) is `talos-*`.
- **Got:** `fx-tests: bug 1809667 has 681 annotations on trunk, but none match the filter` (exit non-zero, empty stdout).
- **Suggestion:** Make `--config` match `testSuite`/the job name, or have `--help` say which field it matches.
- **Example from:** [intermittent/bugs/1809667.talos.files/fx-tests-feedback.md](reports/intermittent/bugs/1809667.talos.files/fx-tests-feedback.md)

## 169. `test --coverage` text lacks per-config names and run counts (2)

`fx-tests test --coverage` text prints only per-platform "N/M ran" lines. Per-config run counts are only in `--json`, so the agent couldn't directly read "5 failures in N Android runs".

- **Question:** How many runs on one platform (android) in total, across its configs?
- **Command:** `fx-tests test dom/serviceworkers/test/test_importscript.html --coverage`
- **Expected:** A per-config table of runs/pass/fail, or a per-platform run total.
- **Got:** Only `android  21/28 ran — 7 scheduled but skipped`; per-config run counts only in `--json` (`.coverage.configs[]`).
- **Suggestion:** List every config with its run count under "Scheduled on", or add a per-platform run total.
- **Example from:** [intermittent/dom/serviceworkers/test/test_importscript.html.files/fx-tests-feedback.md](reports/intermittent/dom/serviceworkers/test/test_importscript.html.files/fx-tests-feedback.md)

## 170. `test --history` gives no hint that failures predate the window (2)

When a test fails from the first day of the window, `fx-tests test <path> --history` is flat and cannot say whether the failure started with an earlier landing; checking took 12 downloaded logs.

- **Question:** Did this test fail before the data window started (landing of bug 2036593 on 2026-08-27)?
- **Command:** `fx-tests test toolkit/mozapps/update/tests/unit_update_binary/marFailurePartial.js --history`
- **Expected:** Whether the failure started before the window, e.g. a "failing since before the window" note.
- **Got:** History flat from the window's first day (2026-08-31), with no note that the onset is older than the data.
- **Suggestion:** Say "failing since before the window" and offer a small pre-window sample of older jobs on the failing configs.
- **Example from:** [mac/toolkit/mozapps/update/tests/unit_update_binary/marFailurePartial.js.files/fx-tests-feedback.md](reports/mac/toolkit/mozapps/update/tests/unit_update_binary/marFailurePartial.js.files/fx-tests-feedback.md)

## 171. `task --messages` silently drops messages, omitting the real first failure (2)

`fx-tests task <id> --messages` showed 5 messages with no "+N more" line, while `--json` `allMessages` has 7; the omitted ones included the actual first failure (a TypeError), and a leak line was chosen as headline.

- **Question:** What are all failure messages for this test in this job?
- **Command:** `fx-tests task bCWxwzOCQrixDHeX-SDAjg --messages`
- **Expected:** Every failure message for `browser_markup_load_01.js` (help says "cap 20"), or a "+N more" line; first failure first.
- **Got:** 5 messages (unexpected popup, 4 leak lines), no "+N more"; `Uncaught exception in test - TypeError: ... this.targetFront is null` omitted.
- **Suggestion:** Show all messages up to the cap, add "+N more" when truncated, and order chronologically.
- **Example from:** [devtools/devtools/client/inspector/markup/test/browser_markup_load_01.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/inspector/markup/test/browser_markup_load_01.js.files/fx-tests-feedback.md)

## 172. `test --task-ids --limit N` shows the oldest tasks, not the newest (2)

`fx-tests test <path> --task-ids --limit 12` returned the 12 oldest failing tasks, from the first day of the window. Recent tasks, whose artifacts are still live, were cut off.

- **Question:** What are the most recent failing tasks of this test?
- **Command:** `fx-tests test <path> --task-ids --limit 12`
- **Expected:** The newest failing tasks (recent tasks have live artifacts and match current code).
- **Got:** The 12 oldest, from 2026-09-01; the newest are behind `… 800 more`.
- **Suggestion:** List newest first, or add a `--sort recent` option.
- **Example from:** [browser/browser/components/customizableui/test/browser_885052_customize_mode_observers_disabed.js.files/fx-tests-feedback.md](reports/browser/browser/components/customizableui/test/browser_885052_customize_mode_observers_disabed.js.files/fx-tests-feedback.md)

## 173. `task` counts a manifest SKIP as an execution (2)

`fx-tests task --profiles` reported "1 failing execution of 3" where the "of 3" counts a `run-if = android` SKIP as an execution. A related report found `task --passed` text output mixes SKIP with PASS, so a skipped test was counted as run.

- **Question:** Did this test pass when the harness reran it, and how many times did it actually run?
- **Command:** `fx-tests task IYyCUf1YSa6jUovnDSBhMA --profiles --limit 0`
- **Expected:** "Passed when the harness reran it.", with the SKIP not counted as an execution.
- **Got:** "TIMEOUT — 1 failing execution of 3 / Test timed out"; the "of 3" counts the `SKIP` from `xpcshell.toml`.
- **Suggestion:** Exclude manifest SKIPs from execution counts, and label SKIP separately from PASS in `--passed`.
- **Example from:** [mac/toolkit/components/extensions/test/xpcshell/test_ext_webRequest_mergecsp.js.files/fx-tests-feedback.md](reports/mac/toolkit/components/extensions/test/xpcshell/test_ext_webRequest_mergecsp.js.files/fx-tests-feedback.md)

## 174. `failures` rows lack test paths and a message-substring filter (2)

To find other tests failing with the same message, the agent had to grep the full tree-wide `fx-tests failures` list. Rows show the message and a test count but not which tests.

- **Question:** Which other tests fail with this same message?
- **Command:** `fx-tests failures --harness mochitest --limit 0 --full-messages | grep "content window should be garbage collected"`
- **Expected:** A message-substring filter on `failures` (as `errors --message`), and the test path on each row.
- **Got:** Thousands of rows to grep; rows show message and test count but not which test.
- **Suggestion:** `fx-tests failures --message <substring>`, listing the test paths behind each matching message.
- **Example from:** [mac/dom/indexedDB/test/test_event_listener_leaks.html.files/fx-tests-feedback.md](reports/mac/dom/indexedDB/test/test_event_listener_leaks.html.files/fx-tests-feedback.md)

## 175. `intermittent --bug` ranks harness "finished in <n>ms" as the top failure message (2)

`fx-tests intermittent --bug 2067919` put the harness's per-test duration line at the top of "Failure messages". The bug's real failure, an OOM PROCESS-CRASH in 75 win32 jobs, carried no line and didn't appear at all.

- **Question:** What did the jobs annotated on this bug actually fail on?
- **Command:** `fx-tests intermittent --bug 2067919 --since 21`
- **Expected:** The bug's own OOM PROCESS-CRASH in "Failure messages", and a count of occurrences carrying no line.
- **Got:** `543x finished in <n>ms` on top, then leaks; nothing says 75 of 626 occurrences had no line.
- **Suggestion:** Drop `finished in <n>ms` / `profile uploaded in ...` from the ranking; print "N occurrences carried no TEST-UNEXPECTED-FAIL line (crash-only?)".
- **Example from:** [intermittent/bugs/2067919.files/fx-tests-feedback.md](reports/intermittent/bugs/2067919.files/fx-tests-feedback.md)

## 176. `fx-tests task --messages` shows a crash signature under a "Test timed out" message (2)

For a CRASH+TIMEOUT execution, `fx-tests task --messages` puts the shutdown crash signature under the "Test timed out" message, mixing two events. It gives no minidump ID, so `fx-tests crash` can't be used.

- **Question:** Which process crashed in this CRASH execution, and with what stack?
- **Command:** `fx-tests task STEiuzNkS7-YEkXQUu0XNg --messages`
- **Expected:** The minidump ID of the CRASH execution, or at least the crashing process type.
- **Got:** "CRASH, TIMEOUT — 2 failing executions of 3" with the signature under "Test timed out"; `test --task-ids` lists it only as TIMEOUT.
- **Suggestion:** Show the crash separately from the timeout message, with its minidump ID and process type.
- **Example from:** [intermittent/dom/media/webspeech/recognition/test/browser_speech_recognition_telemetry.js.bug2072051.files/fx-tests-feedback.md](reports/intermittent/dom/media/webspeech/recognition/test/browser_speech_recognition_telemetry.js.bug2072051.files/fx-tests-feedback.md)

## 177. With `test --config`, Issues counts exceed the config's fail count (2)

With `fx-tests test <path> --config`, the header gave 117 fails but Issues row 1 said 156x. The Issues block seems to count every config, so one config's failure modes couldn't be read.

- **Question:** What are the failure modes of this test on one config?
- **Command:** `fx-tests test browser/base/content/test/siteIdentity/browser_geolocation_indicator.js --config test-windows11-32-25h2/opt-mochitest-browser-chrome --exclude-config shippable,msix,standalone,asan,ccov`
- **Expected:** Issues counts restricted to that config.
- **Got:** The header says `117 fail`, but Issues row 1 says `156x`.
- **Suggestion:** Make `--config` filter the Issues block.
- **Example from:** [browser/browser/base/content/test/browser-siteIdentity/browser_identityBlock_flicker.js.files/fx-tests-feedback.md](reports/browser/browser/base/content/test/browser-siteIdentity/browser_identityBlock_flicker.js.files/fx-tests-feedback.md)

## 178. `task --profiles` says max-duration kill for a job aborted for memory (2)

`fx-tests task <id> --profiles` claimed the job was killed for exceeding its maximum duration, but it ran 8m9s of 30 min and generic-worker aborted it for memory, sending readers after the wrong cause.

- **Question:** Why did this job end?
- **Command:** `fx-tests task FijKq8dzSKy6l-KjfvuZTQ --profiles`
- **Expected:** The reason the job ended, as generic-worker logged it.
- **Got:** "task FijKq8dzSKy6l-KjfvuZTQ.0 was killed for exceeding its maximum duration"; the log says `task aborted due to sustained memory usage above 90% ...`.
- **Suggestion:** Report the generic-worker resolution line (max run time, memory guard, exit status) as the reason.
- **Example from:** [intermittent/bugs/1990712.files/fx-tests-feedback.md](reports/intermittent/bugs/1990712.files/fx-tests-feedback.md)

## 179. Failures "logged result after SimpleTest.finish()" invisible to `test` and `task` (2)

Unexpected FAILs logged after `SimpleTest.finish()` leave test_end at PASS, so `fx-tests test` reports the test as passing and `fx-tests task` shows no failure, though sheriffs starred 12 jobs in 7 days.

- **Question:** How often does this test fail, and in which jobs?
- **Command:** `fx-tests test dom/security/test/sec-fetch/test_trustworthy_loopback.html`
- **Expected:** The 12 starred jobs counted as failures, with `task` listing the TEST-UNEXPECTED-FAIL lines.
- **Got:** "Verdict: passing. 13,484 of 13,484 runs passed"; `task` says "0 failing ... No test-level failure in this job".
- **Suggestion:** Count TEST-UNEXPECTED-FAIL lines under a PASS test_end as a failure (or a separate row), and list them in `task`.
- **Example from:** [intermittent/dom/security/test/sec-fetch/test_trustworthy_loopback.html.files/fx-tests-feedback.md](reports/intermittent/dom/security/test/sec-fetch/test_trustworthy_loopback.html.files/fx-tests-feedback.md)

## 180. "(finished)" PROCESS-CRASH shown by `fx-tests task` but not counted by `test`/`crashes` (2)

A PROCESS-CRASH naming a test "(finished)" is listed by `fx-tests task` as a CRASH, but `fx-tests test` shows 0 crashes and `fx-tests crashes` doesn't count it, so the test looks clean.

- **Question:** Does this test crash on windows11-32 debug?
- **Command:** `fx-tests test browser/components/aiwindow/ui/test/browser/browser_aiwindow_group_tabs_button.js --history --config windows11-32-25h2/debug`
- **Expected:** The same attribution as `fx-tests task PRIon_aeR72MsHGuLcpkCw`, which lists `CRASH @ CCGraphBuilder::AddNode`.
- **Got:** 277 pass, 0 crash; `fx-tests crashes --path browser/components/aiwindow --signature CCGraphBuilder` does not count it either.
- **Suggestion:** Use the same attribution in both, or have the aggregate say "(finished)" crashes are excluded.
- **Example from:** [intermittent/bugs/2067919.files/fx-tests-feedback.md](reports/intermittent/bugs/2067919.files/fx-tests-feedback.md)

## 181. `test` Issues lists a manifest `run-if` exclusion as a SKIP issue (2)

`fx-tests test` listed `188x SKIP http3` under Issues for a manifest-wide `run-if = ["!http3"]`, without distinguishing this scoping from a disabled test; history showed skips dropping to 0 without saying why.

- **Question:** Is this SKIP issue a disabled test, or a `run-if` scoping exclusion?
- **Command:** `fx-tests test devtools/client/netmonitor/test/new-resend-request/browser_net_edit_resend_cancel.js`
- **Expected:** The Issues row, or the history, to say the skip is a `run-if` (or that the skip was converted).
- **Got:** `1.   188x  SKIP    http3` under "Issues", not told apart from a disabled test.
- **Suggestion:** Mark `run-if` exclusions (or converted skips) as such in Issues/history, or give SKIP issues a "last seen" date.
- **Example from:** [devtools/devtools/client/netmonitor/test/new-resend-request/browser_net_edit_resend_cancel.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/netmonitor/test/new-resend-request/browser_net_edit_resend_cancel.js.files/fx-tests-feedback.md)

## 182. Perftest-only test reads "skipped everywhere" in `test`, perftest runs invisible (2)

`fx-tests test` on a mochitest that runs only under mozperftest says `Verdict: skipped everywhere in this window`. Nothing says perftest jobs aren't in the data, which hid a perma-failure with 106 annotations.

- **Question:** Does this test fail in its perftest jobs, and has it passed since the fix?
- **Command:** `fx-tests test browser/extensions/formautofill/test/browser/performance/browser_formautofill_ml_perf.js`
- **Expected:** The runs of this test, which runs only under mozperftest and perma-failed there on 2026-09-15.
- **Got:** `0 runs ... 1,915 skip`, `Verdict: skipped everywhere in this window`, skip reason "Disabled because this is only run as a performance test".
- **Suggestion:** A verdict line saying "runs only as a perftest; perftest jobs are not in this data".
- **Example from:** [intermittent/browser/extensions/formautofill/test/browser/performance/browser_formautofill_ml_perf.js.files/fx-tests-feedback.md](reports/intermittent/browser/extensions/formautofill/test/browser/performance/browser_formautofill_ml_perf.js.files/fx-tests-feedback.md)

## 183. `--durations` gives only min/median/p95/max, no per-run values (2)

`fx-tests test <path> --durations` (text or `--json`) gives only min/median/p95/max per config, so it can't say how many passing runs exceeded a threshold or which tasks were slowest.

- **Question:** Did passing runs on macOS 10.15 also hit the same ~30 s stall?
- **Command:** `fx-tests test netwerk/test/unit/test_webtransport_stop_sending.js --durations --json --config macosx1015`
- **Expected:** How many passing runs took about 30 s or more on macOS 10.15.
- **Got:** Only min / median / p95 / max per config; the JSON has no per-run durations either.
- **Suggestion:** A count of passing runs above a threshold (`--slower-than 20s`), or the task IDs of the slowest passing runs.
- **Example from:** [mac/netwerk/test/unit/test_webtransport_stop_sending.js.files/fx-tests-feedback.md](reports/mac/netwerk/test/unit/test_webtransport_stop_sending.js.files/fx-tests-feedback.md)

## 184. Crash signatures keep `panic_hook`/`MOZ_CrashSequence` frames, splitting one panic into rows (1)

`fx-tests test <path>` listed one Glean panic as three Issues rows: `CRASH @ <mozglue_static::panic_hook ...`, `CRASH @ MOZ_CrashSequence`, and a "Failure details not recorded" FAIL for ASan/TSan builds.

- **Question:** What is this crash, beyond the Rust panic plumbing?
- **Command:** `fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_defaultPrefs.js`
- **Expected:** One failure mode for one Glean panic (`No database found` on the glean.init thread).
- **Got:** Three rows (3x each): `CRASH @ <mozglue_static::panic_hook ...`, `CRASH @ MOZ_CrashSequence`, `FAIL Failure details not recorded (likely Android or platform logging issue)`.
- **Suggestion:** Skip panic_hook/MOZ_Crash*/RustMozCrash frames in signatures, show the crash reason, and use `Hit MOZ_CRASH(...)` for sanitizer FAILs.
- **Example from:** [browser/browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_defaultPrefs.js.files/fx-tests-feedback.md](reports/browser/browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_defaultPrefs.js.files/fx-tests-feedback.md)

## 185. fx-tests reads stdin, breaking `while read` loops (1)

In a `while read` loop over 159 task IDs, the first `fx-tests task` call consumed the rest of stdin. Only about 9 tasks were processed, and no error was shown.

- **Question:** What are the failure messages of every task in a list, one `fx-tests task` call per line?
- **Command:** `while read t c; do fx-tests task $t --messages; done < list.txt`
- **Expected:** One call per line of the list.
- **Got:** The first `fx-tests` call consumed the rest of stdin, so only about 9 of the 159 tasks were processed, without any error.
- **Suggestion:** Don't read stdin (workaround used: `< /dev/null`).
- **Example from:** [intermittent/toolkit/components/downloads/test/unit/test_DownloadCore.js.bug2049776.files/fx-tests-feedback.md](reports/intermittent/toolkit/components/downloads/test/unit/test_DownloadCore.js.bug2049776.files/fx-tests-feedback.md)

## 186. `try` in-row "… 1 more task" lines don't mention `--limit 0` (1)

`fx-tests try <rev> --profiles --task-ids` showed five of six failing tasks with "… 1 more task" / "… 1 more profile" inside the row. The agent went to `--json` and Python before noticing `--limit 0` lists them all.

- **Question:** What are the task IDs and profile URLs of one test on a try push?
- **Command:** `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids`
- **Expected:** The six failing tasks of `browser_tab_splitview_contextmenu.js` with their per-test profile URLs.
- **Got:** Five tasks and "… 1 more task" / "… 1 more profile" inside the row.
- **Suggestion:** Put the `--limit 0` hint on the truncated line, or print all when only a handful remain.
- **Example from:** [mac/browser/components/tabbrowser/test/browser/tabs/browser_tab_splitview_contextmenu.js.files/fx-tests-feedback.md](reports/mac/browser/components/tabbrowser/test/browser/tabs/browser_tab_splitview_contextmenu.js.files/fx-tests-feedback.md)

## 187. `crash --thread` accepts only an index, not a thread name (1)

The agent wanted to know what the `Renderer` thread was doing in several dumps. `fx-tests crash --thread` requires an index, which differs between dumps, so each dump needed an `--all-threads` run and a grep first.

- **Question:** What was the Renderer thread doing in each dump?
- **Command:** `fx-tests crash XxpEEKc3Rd68tEAKdmJnDw.0 0E3F9799-… --thread 9 --frames 0`
- **Expected:** `--thread Renderer` (by name) to work.
- **Got:** An index is required, and it differs between dumps.
- **Suggestion:** Accept a thread name for `--thread`.
- **Example from:** [browser/browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_topPicks.js.files/fx-tests-feedback.md](reports/browser/browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_topPicks.js.files/fx-tests-feedback.md)

## 188. `fx-tests failures <dir>` rejects a positional path (1)

`fx-tests failures` refuses a positional directory argument, while `test`, `try --test` and similar commands take a path. Minor: the error suggested `--path`.

- **Question:** Which failures are recorded under devtools/client/storage/test?
- **Command:** `fx-tests failures devtools/client/storage/test --harness mochitest`
- **Expected:** The same as `--path devtools/client/storage/test`, like other commands accepting a path.
- **Got:** "failures takes no positional arguments".
- **Example from:** [devtools/devtools/client/storage/test/browser_storage_delete_all.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/storage/test/browser_storage_delete_all.js.files/fx-tests-feedback.md)

## 189. `fx-tests failures` gives no task IDs or job count behind a message (1)

`fx-tests failures --message` lists tests and counts but no task IDs or number of jobs. Finding the few broken jobs behind 744 failures in 372 tests took about 10 commands.

- **Question:** Which jobs produced the 744 `NS_ERROR_FILE_CORRUPTED ... readUserPrefsFromFile` failures in 372 tests?
- **Command:** `fx-tests failures --harness xpcshell --message NS_ERROR_FILE_CORRUPTED --json --limit 0`
- **Expected:** The task IDs (or at least the job count / configs) behind the message.
- **Got:** Tests and counts only ("This file records no job names").
- **Suggestion:** Show "from N jobs" with task IDs, or add `fx-tests failures --message X --task-ids`.
- **Example from:** [devtools/devtools/shared/security/tests/xpcshell/test_devtools_socket_status.js.prefs-corrupted.files/fx-tests-feedback.md](reports/devtools/devtools/shared/security/tests/xpcshell/test_devtools_socket_status.js.prefs-corrupted.files/fx-tests-feedback.md)

## 190. `issues --type timeout` ignored with `--group-by message` (1)

While looking for other tests hanging with "application timed out after 370.0 seconds", `fx-tests issues --type timeout --group-by message` printed the same FAIL-dominated table as without `--type`. The filter did apply with `--group-by test`.

- **Question:** Which timeout messages are most common tree-wide?
- **Command:** `fx-tests issues --type timeout --group-by message`
- **Expected:** Only timeout issues, grouped by message.
- **Got:** The same FAIL-dominated table as without `--type`.
- **Suggestion:** Apply `--type` with `--group-by message`.
- **Example from:** [intermittent/browser/components/genai/tests/browser/browser_link_preview_telemetry.js.files/fx-tests-feedback.md](reports/intermittent/browser/components/genai/tests/browser/browser_link_preview_telemetry.js.files/fx-tests-feedback.md)

## 191. `errors --message` calls an unattributed message "specific to that test" (1)

`fx-tests errors --message` gave the verdict "specific to that test" for a shutdown message whose only "test" is the empty `(not attributed to a test)` path, the opposite of the truth.

- **Question:** Is this shutdown-hang message specific to one test or ambient?
- **Command:** `fx-tests errors --message "hanging at shutdown; attempting crash report"`
- **Expected:** The message, logged at browser shutdown outside any test, reported as unattributed.
- **Got:** `34 occurrences in 1 test — specific to that test, not ambient noise`, where the one test is `(not attributed to a test)`.
- **Suggestion:** Report messages whose only path is empty as unattributed, not test-specific.
- **Example from:** [intermittent/dom/media/webspeech/recognition/test/browser_speech_recognition_process_lifetime.js.files/fx-tests-feedback.md](reports/intermittent/dom/media/webspeech/recognition/test/browser_speech_recognition_process_lifetime.js.files/fx-tests-feedback.md)

## 192. `task --json` failure order (by failure count, then path) undocumented (1)

`fx-tests task --json` sorts failures by failure count then path, which looks alphabetical. Taking `failures[0]` as the first failure named the wrong test in 4 jobs, nearly reporting a second leaker.

- **Question:** Which test in this job failed first (the leaker suspect)?
- **Command:** `fx-tests task <taskId> --json --limit 0`
- **Expected:** `failures` in path order, as the text output appeared alphabetical.
- **Got:** Ordered by failure count first, then path: a 2/2 test comes before an alphabetically earlier 1/2 test.
- **Suggestion:** Document the sort order in `--help`, or add a per-failure start time.
- **Example from:** [mac/browser/components/extensions/test/browser/browser_ext_runtime_getContexts.js.files/fx-tests-feedback.md](reports/mac/browser/components/extensions/test/browser/browser_ext_runtime_getContexts.js.files/fx-tests-feedback.md)

## 193. `fx-tests crash --all-threads` prints identical thread stacks in full (1)

`fx-tests crash --all-threads` printed 670 lines, mostly 12 WRWorker/WRWorkerLP threads with the same rayon sleep stack, each in full with every inlined std frame.

- **Question:** What were all threads of this crash dump doing?
- **Command:** `fx-tests crash LtzWWLsdQga6nv0JV6ne7Q.0 BFAA6A55-23C4-44DC-ABAA-4CAD248FF0BE --all-threads --frames 15`
- **Expected:** Compact output, with threads sharing a stack shown once.
- **Got:** 670 lines, most of them 12 WRWorker/WRWorkerLP threads with the same rayon sleep stack, each printed in full with every inlined std frame.
- **Suggestion:** Collapse identical stacks ("WRWorker#0-3, WRWorkerLP#0-7: same stack") and optionally hide inlined `std::`/rustlib frames.
- **Example from:** [browser/browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_market.js.files/fx-tests-feedback.md](reports/browser/browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_market.js.files/fx-tests-feedback.md)

## 194. `fx-tests test` Issues print full 60-line stacks per issue (1)

`fx-tests test` printed each stack-bearing Issue message in full: 34 KB of output, with 60-line stacks reprinted in "same as N, but" variants, and every flag re-printing the whole block, instead of one or two lines per issue.

- **Question:** What are this test's failure modes (and, with `--history`, where is the history table)?
- **Command:** `fx-tests test devtools/client/inspector/grids/test/browser_grids_grid-list-on-iframe-reloaded.js`
- **Expected:** One or two lines per issue, as the width-cut rule in `guide` suggests.
- **Got:** 34 KB of output; issues 3–12 (`uncaught rejection: Connection closed ... getFragments`) each print their full 60-line request stack, and "↑ same as 3, but" variants reprint it.
- **Suggestion:** Cut stack-bearing Issue messages to one or two lines.
- **Example from:** [devtools/devtools/client/inspector/grids/test/browser_grids_grid-list-on-iframe-reloaded.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/inspector/grids/test/browser_grids_grid-list-on-iframe-reloaded.js.files/fx-tests-feedback.md)

## 195. `test <manifest.toml>` refuses a manifest path (1)

`fx-tests test` given a `mochitest.toml` manifest says no test path contains it instead of reporting the manifest's tests. The agent had to query one test file from the manifest instead.

- **Question:** How do the tests of this manifest run (e.g. never on Android)?
- **Command:** `fx-tests test dom/media/webspeech/recognition/test/mochitest.toml`
- **Expected:** The manifest's tests, grouped.
- **Got:** "No test path in the xpcshell and mochitest 21-day data contains ...mochitest.toml".
- **Suggestion:** Accept a manifest path and report its tests, grouped.
- **Example from:** [intermittent/bugs/1667271.files/fx-tests-feedback.md](reports/intermittent/bugs/1667271.files/fx-tests-feedback.md)

## 196. `test --bugs` only checks the last 7 days, not the failures' dates (1)

`fx-tests test <path> --bugs` ranks annotations only for the last 7 days; all this test's failures were older, so a bug starred on them would be missed, and nothing is printed either way.

- **Question:** Which bug, if any, did sheriffs star this test's failures on?
- **Command:** `fx-tests test devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-values.js --bugs`
- **Expected:** A Bugs section ("none" if empty) covering the window the failures are in.
- **Got:** No Bugs section; only `--progress` stderr showed annotations ranked for 2026-09-16..2026-09-22, while all 5 failures are 2026-08-31 to 2026-09-10.
- **Suggestion:** Look up annotations over the dates of the test's failures, not a fixed last-7-days window.
- **Example from:** [devtools/devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-values.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-values.js.files/fx-tests-feedback.md)

## 197. `fx-tests test` takes one path per call; no multi-path query (1)

To check a list of 362 tests for CI failures and try-push failures, the agent ran `fx-tests test <path> --json` once per test plus `issues` and `try`, and joined the outputs by script.

- **Question:** Which of a list of tests have any failure data, in CI or on a try push?
- **Command:** `fx-tests test <path> --json`
- **Expected:** One command taking a list of test paths, answering per test: CI failures, try push failures, failure profile existence.
- **Got:** three outputs joined by a script; `test` takes one path per call (fast, 0.4 s each, so the loop was cheap).
- **Suggestion:** Accept several paths: `fx-tests test <path>... --try <rev>`, or `try <rev> --test` with multiple paths listing ones with no failure.
- **Example from:** [devtools/parent.files/fx-tests-feedback.md](reports/devtools/parent.files/fx-tests-feedback.md)

## 198. `test --task-ids` lists fewer tasks than the summary's crash count (1)

`fx-tests test <path>` reported `3 crash`, but `--task-ids` listed only 2 tasks and said nothing about the missing one.

- **Question:** Which tasks had this test's 3 crashes?
- **Command:** `fx-tests test dom/animation/test/chrome/test_animation_observers_async.html --task-ids --limit 0`
- **Expected:** 3 tasks, since the summary says `3 crash`.
- **Got:** 2 tasks (FBsWw41pQhCdvfAyUY-58A.0, RJ8Cx0mQS4eRxHNF7Euv1A.0), and no line saying why the third is missing.
- **Example from:** [intermittent/bugs/2060253.files/fx-tests-feedback.md](reports/intermittent/bugs/2060253.files/fx-tests-feedback.md)

## 199. `task --messages` omits node test-server stderr logged during the failure (1)

For a `child process exit closing code: 1` failure, `fx-tests task --messages` showed only the xpcshell message. The cause, `node moz-http2 [stderr] Error: listen EADDRINUSE`, was only in live_backing.log, so the agent downloaded the 16 MB log and grepped it.

- **Question:** Why did the node test server exit?
- **Command:** `fx-tests task <taskId> --messages`
- **Expected:** The cause of `child process exit closing code: 1`.
- **Got:** Only the xpcshell message; the `node moz-http2 [stderr] Error: listen EADDRINUSE ... :::<port>` line is not shown.
- **Suggestion:** Surface `node moz-http2 [stderr]` lines emitted during a failing test.
- **Example from:** [intermittent/netwerk/test/unit/test_http3_0rtt_mitigations.js.files/fx-tests-feedback.md](reports/intermittent/netwerk/test/unit/test_http3_0rtt_mitigations.js.files/fx-tests-feedback.md)

## 200. `test --task-ids --json` has null `message` for the most recent day (1)

In `fx-tests test --task-ids --json`, the entries of the latest day had `"message": null`, while older entries carried the message.

- **Question:** Which failure message does each failing task have?
- **Command:** `fx-tests test browser/components/places/tests/browser/browser_bookmarks_change_title.js --task-ids --limit 0 --json`
- **Expected:** The message on every entry.
- **Got:** `"message": null` for the 14 entries dated 2026-09-21, while older entries carry the message.
- **Example from:** [browser/browser/components/places/tests/browser/browser_bookmarksProperties.js.files/fx-tests-feedback.md](reports/browser/browser/components/places/tests/browser/browser_bookmarksProperties.js.files/fx-tests-feedback.md)

## 201. `test --history` counts `fail-if` expected failures as clean passes (1)

While the manifest had `fail-if = ["a11y_checks"]` (2026-09-11 to 09-16), `fx-tests test --history --config a11y` counted every run as a pass. Nothing marked the ~180 runs that failed as expected.

- **Question:** Were these passes clean, or expected failures under `fail-if`?
- **Command:** `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-watchpoints.js --history --config a11y`
- **Expected:** Expected-failure runs counted apart from clean passes, or the `fail-if` shown under Issues like `skip-if`.
- **Got:** "278 pass (100.00%)", nothing marking the ~180 expected failures. Workaround: `git log` on the manifest.
- **Suggestion:** Count `fail-if` expected failures separately, or show the `fail-if` condition like `skip-if` conditions.
- **Example from:** [devtools/devtools/client/debugger/test/mochitest/browser_dbg-watchpoints.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/debugger/test/mochitest/browser_dbg-watchpoints.js.files/fx-tests-feedback.md)

## 202. `fx-tests task` doesn't name the unfinished test of a job killed by mozharness (1)

For a job killed by "mozharness timed out after 1000 seconds", `fx-tests task` reports no test-level failure. It doesn't name the test that started and never ended, and `test` counts that test as passing.

- **Question:** Which test was running when mozharness killed the job?
- **Command:** `fx-tests task UKBqeZpIT1e3eWPVYHBCmA --profiles`
- **Expected:** The test with a `TEST-START` and no end (here `dom/tests/browser/browser_noopener.js`).
- **Got:** "300 tests, 300 executions, 0 failing ... No test-level failure in this job ... Read the log."
- **Suggestion:** Add an "unfinished test" row in `task`, and count such runs in `test`.
- **Example from:** [intermittent/bugs/1851682.files/fx-tests-feedback.md](reports/intermittent/bugs/1851682.files/fx-tests-feedback.md)

## 203. Jobs failing before any test runs are invisible in `test --history` counts (1)

On a config with 40 failed jobs (application timeout at launch), `fx-tests test ... --history --config` reported all runs passing. A job that never started a test adds nothing to the counts.

- **Question:** How many jobs of this config failed before running any test?
- **Command:** `fx-tests test remote/shared/listeners/test/browser/browser_ChromeWindowListener.js --history --config macosx1500-aarch64-vms`
- **Expected:** Some sign of 40 failed jobs on this config between 2026-09-16 and 09-21.
- **Got:** `257 of 257 runs passed`; the only trace is fewer passes per day (13, 4, 7, 2, 3 against 15-24 before).
- **Suggestion:** Show a per-config count of jobs with no test results.
- **Example from:** [intermittent/bugs/2022292.files/fx-tests-feedback.md](reports/intermittent/bugs/2022292.files/fx-tests-feedback.md)

## 204. No way to see previous/next jobs on the same worker (1)

Nothing in fx-tests shows which jobs ran just before or after a failing job on the same worker, needed for a harness failure suspected to be left over by a previous job.

- **Question:** What ran on this worker just before the failing job?
- **Command:** `fx-tests task fyVJhiPATBKHsGLAXuMFzA --profiles`
- **Expected:** The previous and next jobs on the same worker (task id, run, job name, result, end time).
- **Got:** Nothing in fx-tests answers it; worker `recentTasks` covers only ~12 h. Needed 230 Treeherder `jobs/?machine_name=` requests.
- **Suggestion:** `fx-tests task <taskId> --neighbours`, or worker and "previous job on this worker" columns in `intermittent --bug`.
- **Example from:** [intermittent/bugs/915212.address-in-use.files/fx-tests-feedback.md](reports/intermittent/bugs/915212.address-in-use.files/fx-tests-feedback.md)

## 205. `test` Issues merge identical messages from different check sites (1)

Two identical `ok()` calls at different lines produce the same message, so `fx-tests test` shows one 78x Issue row with no stack. Telling them apart meant grepping 13 logs.

- **Question:** Which of the two identical `ok()` calls produced the failure in each failing run?
- **Command:** `fx-tests test layout/style/test/test_animations_omta_scroll.html --task-ids --limit 0`
- **Expected:** Failures split or labelled by check site (file_animations_omta_scroll.html:200 vs :254).
- **Got:** One Issue row, 78x, no stack; the two sites are indistinguishable.
- **Suggestion:** Group Issues by message + first test-file stack frame, or show that frame.
- **Example from:** [intermittent/layout/style/test/test_animations_omta_scroll.html.files/fx-tests-feedback.md](reports/intermittent/layout/style/test/test_animations_omta_scroll.html.files/fx-tests-feedback.md)

## 206. `fx-tests test` Issues "same as 1, but" collapse merges distinct failure modes (1)

`fx-tests test` collapsed issue 2 as "↑ same as 1, but ..." because it shares a prefix with issue 1, hiding that it is a different failure mode (backward jump vs repeated offset).

- **Question:** Which failing task showed which failure message?
- **Command:** `fx-tests test gfx/layers/apz/test/mochitest/test_group_keyboard.html --task-ids --limit 0`
- **Expected:** Each task ID next to its message(s), so the two failure modes can be told apart.
- **Got:** Issues collapsed as "↑ same as 1, but ...", then a date-grouped task list with no messages; issue 2 ("nondecreasing ... 422.8 → 268.2") differs from issue 1.
- **Suggestion:** `--task-ids` printing the task IDs under each issue (or the messages under each task).
- **Example from:** [mac/gfx/layers/apz/test/mochitest/test_group_keyboard.html.files/fx-tests-feedback.md](reports/mac/gfx/layers/apz/test/mochitest/test_group_keyboard.html.files/fx-tests-feedback.md)

## 207. `test` per-config table counts a crash as a fail, disagreeing with the summary (1)

`fx-tests test` summary said `440 fail ... 1 crash`, but the per-config table counted the crash as a fail, so the table summed to 441, muddling how many runs failed the diagnosed way.

- **Question:** How many runs failed the way I diagnosed?
- **Command:** `fx-tests test <path>`
- **Expected:** The same counting in the summary and the per-config table.
- **Got:** Summary `440 fail ... 1 crash`, but the table counts the crash as a fail (`test-windows11-32-25h2/opt-mochitest-browser-chrome  0.1%  1`), summing to 441.
- **Suggestion:** Show crashes in their own column in the per-config table, or count them the same way in both places.
- **Example from:** [browser/browser/components/aiwindow/ui/test/browser/browser_smartwindow_chat_browser_tabbable.js.files/fx-tests-feedback.md](reports/browser/browser/components/aiwindow/ui/test/browser/browser_smartwindow_chat_browser_tabbable.js.files/fx-tests-feedback.md)

## 208. `test --bugs` errors "not found in manifest" for a test in its manifest (1)

`fx-tests test <path> --bugs` prints the summary, then a `not found in manifest` error, although the test is in `browser.toml` at that revision. The manifest lookup for the revision seems to fail (hg.mozilla.org returned 404s at the time).

- **Question:** Which bugs name this test?
- **Command:** `fx-tests test browser/components/asrouter/tests/browser/browser_trigger_nimbusUpdate.js --bugs`
- **Expected:** The bugs naming the test, or "none".
- **Got:** `error: browser/components/asrouter/tests/browser/browser_trigger_nimbusUpdate.js@2981cf3b...: not found in manifest`.
- **Example from:** [browser/browser/components/asrouter/tests/browser/browser_trigger_nimbusUpdate.js.files/fx-tests-feedback.md](reports/browser/browser/components/asrouter/tests/browser/browser_trigger_nimbusUpdate.js.files/fx-tests-feedback.md)

## 209. `task` doesn't point to the build task or its generated files (1)

A warning named `StaticComponents.cpp:15659`, a generated file absent from hg. Resolving it meant finding the build task by hand and downloading its 21 MB generated-files tarball; `fx-tests task` gives no pointer.

- **Question:** Which XPCOM component does a `StaticComponents.cpp:<line>` warning name?
- **Command:** *(no fx-tests command answers this)*
- **Expected:** A way to read the generated file's line for the job's build.
- **Got:** No command offered it; the line was `nsUserIdleServiceWin::GetInstance()`, found via `public/build/target.generated-files.tar.gz`.
- **Suggestion:** `fx-tests task <id>` prints the build task id and generated-files URL, or a helper resolves `<generated file>:<line>`.
- **Example from:** [browser/browser/components/profiles/tests/unit/test_create_profile.js.gfx-shutdown.files/fx-tests-feedback.md](reports/browser/browser/components/profiles/tests/unit/test_create_profile.js.gfx-shutdown.files/fx-tests-feedback.md)

## 210. `intermittent --bug` lists per-locale job names separately instead of grouping them (1)

`fx-tests intermittent --bug 2071643 --tree all --since 30` listed jobs one row per locale (806 names at "2x" each), hiding when and where the bug hit; grouping needed a script over `--json`.

- **Question:** When did this bug start and stop, per tree and per push?
- **Command:** `fx-tests intermittent --bug 2071643 --tree all --since 30`
- **Expected:** A per-day or per-push breakdown: 5 central nightlies (09-12 to 09-14), then one beta push (09-21).
- **Got:** 806 job names at "2x" each (`test-update-integrity-en-GB-linux64-shippable`, ...), and a flat, truncated list of occurrences.
- **Suggestion:** Group job names with locale and chunk stripped, and add a per-day history line.
- **Example from:** [intermittent/bugs/2071643.files/fx-tests-feedback.md](reports/intermittent/bugs/2071643.files/fx-tests-feedback.md)

## 211. `test --task-ids` gives no last log line before each CRASH (1)

To find the test step where each of 13 crashes happened, `fx-tests test <path> --task-ids` gave only task and minidump IDs. That meant loading all 13 resource-usage profiles.

- **Question:** At which step of the test did each of the 13 crashes happen (last TEST-PASS/INFO before the crash)?
- **Command:** `fx-tests test <path> --task-ids --limit 0`
- **Expected:** The last log line before each CRASH, per task.
- **Got:** Task and minidump IDs only.
- **Suggestion:** Print the last log line before a CRASH, per task, in `--task-ids` output.
- **Example from:** [intermittent/browser/base/content/test/browser-webrtc/browser_devices_get_user_media_camera_preview.js.files/fx-tests-feedback.md](reports/intermittent/browser/base/content/test/browser-webrtc/browser_devices_get_user_media_camera_preview.js.files/fx-tests-feedback.md)

## 212. `intermittent --bug` merges unsuffixed `mochitest-plain` with its chunked variants (1)

The "Job names, chunk numbers merged" table of `fx-tests intermittent --bug 2020146` merged `mochitest-plain` (a 1-chunk job running all 6802 tests) with `mochitest-plain-N`. The unsuffixed name was the discriminator for 92 of the 167 jobs.

- **Question:** Were these jobs single-chunk?
- **Command:** `fx-tests intermittent --bug 2020146 --since 30 --limit 0`
- **Expected:** Unsuffixed job names kept apart from chunked ones.
- **Got:** "Job names, chunk numbers merged" merged `mochitest-plain` with `mochitest-plain-N`.
- **Suggestion:** Keep unsuffixed job names separate from chunked ones in that table.
- **Example from:** [intermittent/bugs/2020146.files/fx-tests-feedback.md](reports/intermittent/bugs/2020146.files/fx-tests-feedback.md)

## 213. `task` on test-verify jobs lacks per-step (normal vs chaos) durations (1)

On a test-verify job, `fx-tests task` counts executions but doesn't split them by verify step or give durations. The agent had to bucket profile markers with `profiler-cli` and a script. The Linux vs macOS chaos-mode comparison was the key evidence.

- **Question:** How long did this test take in each test-verify step (normal vs chaos)?
- **Command:** `fx-tests task <TV taskId> --json --passed`
- **Expected:** Per test, executions split by verify step with durations, e.g. "normal 10x 8.2 s, chaos 1x 120.5 s TIMEOUT".
- **Got:** Executions counted, no per-step breakdown, no durations.
- **Suggestion:** Split a test's executions by `::: Running test verification step` with outcomes and durations.
- **Example from:** [browser/browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_merino.js.files/fx-tests-feedback.md](reports/browser/browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_merino.js.files/fx-tests-feedback.md)

## 214. `test --task-ids` text output not grouped or narrowable by config (1)

`fx-tests test <path> --task-ids --limit 0` listed 1321 task IDs by day only, so the Windows failures were buried. Getting one platform's tasks needed `--json` and a Python filter on `jobName`.

- **Question:** Which failing tasks are on one platform (the non-macOS-15 ones)?
- **Command:** `fx-tests test <path> --task-ids --limit 0`
- **Expected:** A way to restrict the task list to one config, or the list grouped by config.
- **Got:** 1321 task IDs by day, no config grouping.
- **Suggestion:** Group `--task-ids` by config, or show a per-config sample of task IDs under the config table.
- **Example from:** [browser/browser/components/urlbar/tests/unit/test_search_suggestions.js.files/fx-tests-feedback.md](reports/browser/browser/components/urlbar/tests/unit/test_search_suggestions.js.files/fx-tests-feedback.md)

## 215. `fx-tests test` "saw the failure more than once" count conflicts with `task` (1)

`fx-tests test` says only 9 of 438 failing jobs saw the failure more than once. But both jobs checked with `fx-tests task` show "2 failing executions of 2", so retry-failures seem undercounted.

- **Question:** How often does the harness retry pass (order dependency vs fresh-browser failure)?
- **Command:** `fx-tests test dom/media/test/test_closing_connections.html`
- **Expected:** Jobs whose harness retry also failed counted among the "more than once" jobs.
- **Got:** "9 of 438 failing jobs saw the failure more than once", while `fx-tests task` on two jobs says "TIMEOUT — 2 failing executions of 2".
- **Example from:** [intermittent/dom/media/test/test_closing_connections.html.files/fx-tests-feedback.md](reports/intermittent/dom/media/test/test_closing_connections.html.files/fx-tests-feedback.md)

## 216. `task` doesn't show the GPU vendor/driver recorded in the profile (1)

To learn which GPU driver each failing machine ran, the agent found that `fx-tests task <id> --profiles` shows none, though the per-test profile records it. It downloaded 13 profiles and grepped them, which turned out to be the answer.

- **Question:** Which GPU driver was each failing job's machine running?
- **Command:** `fx-tests task <id> --profiles`
- **Expected:** The adapter and driver version, which the per-test profile already records.
- **Got:** Nothing.
- **Suggestion:** Show GPU vendor, device and driver in `fx-tests task`'s header, taken from the profile's metadata.
- **Example from:** [intermittent/dom/media/test/test_gfx_sanity_gpu.html.files/fx-tests-feedback.md](reports/intermittent/dom/media/test/test_gfx_sanity_gpu.html.files/fx-tests-feedback.md)

## 217. `task` doesn't say which tests were re-executed without failing (1)

`fx-tests task <id> --profiles` reports "49 tests, 67 executions" but not which tests ran twice; the Android harness re-ran a manifest's first 9 tests right before the failing test.

- **Question:** Did the harness re-run tests in this job, and around which test?
- **Command:** `fx-tests task <taskId> --profiles`
- **Expected:** A note when tests ran more than once without failing, and around which test.
- **Got:** "49 tests, 67 executions" but not which tests ran twice; the failing test's row says "1 failing execution of 1".
- **Suggestion:** Show e.g. "Re-executed without failure: widget/tests (9 tests x2)" with the test that ran just before.
- **Example from:** [intermittent/widget/tests/test_mouse_double_click_on_android.html.files/fx-tests-feedback.md](reports/intermittent/widget/tests/test_mouse_double_click_on_android.html.files/fx-tests-feedback.md)

## 218. `intermittent --bug` can't show whether a given test ran in each occurrence (1)

`fx-tests test --history` showed no failure since 2026-09-07 while bug 1991833 kept getting annotations on the same config. Nothing linked them; `task --passed` on 4 jobs showed the test hadn't run there.

- **Question:** Did the job-level failure stop, or did it stop hitting this test?
- **Command:** `fx-tests intermittent --bug 1991833`
- **Expected:** Some way to see that the recent meltdown jobs did not run this test.
- **Got:** Nothing links the two.
- **Suggestion:** Have `fx-tests intermittent --bug` list, per occurrence, whether a given test ran in that job.
- **Example from:** [devtools/devtools/shared/tests/xpcshell/test_LocalizationHelper_missing_key.js.files/fx-tests-feedback.md](reports/devtools/devtools/shared/tests/xpcshell/test_LocalizationHelper_missing_key.js.files/fx-tests-feedback.md)

## 219. `fx-tests intermittent --history` shows zeros before Treeherder retention without saying so (1)

With `--since 150`, `fx-tests intermittent --history` printed zeros before 05-25, which looks like a step change, but 05-25 is where Treeherder's ~120-day retention ends. Nothing says so.

- **Question:** How has this bug's annotation count evolved over the last 150 days?
- **Command:** `fx-tests intermittent --bug 1628960 --since 150 --history`
- **Expected:** Something like "data before 2026-05-25 not retained".
- **Got:** Zeros from 2026-04-26 to 05-24, then counts from 05-25.
- **Suggestion:** Say where retained data begins, e.g. "data before 2026-05-25 not retained".
- **Example from:** [intermittent/bugs/1628960.files/fx-tests-feedback.md](reports/intermittent/bugs/1628960.files/fx-tests-feedback.md)

## 220. `fx-tests test` has no perftest data; `intermittent --bug` doesn't say so (1)

`fx-tests test` on a mozperftest script reports the path as absent from the xpcshell and mochitest data, so there is no per-day pass/fail, and `intermittent --bug` gives no warning for a perftest-only bug.

- **Question:** When did this perftest job stop failing?
- **Command:** `fx-tests test testing/performance/mobile-startup/android_startup_cmff_cvns.py`
- **Expected:** Pass/fail per day for the perftest jobs that run it.
- **Got:** "No test path in the xpcshell and mochitest 21-day data contains ...": perftests are outside the data.
- **Suggestion:** For a bug whose only jobs are perftests, have `intermittent --bug` say up front that `test`/`--history` cannot cover them.
- **Example from:** [intermittent/bugs/2070405.files/fx-tests-feedback.md](reports/intermittent/bugs/2070405.files/fx-tests-feedback.md)

## 221. `test` has no python source-test (mozbase) data (1)

`fx-tests test` on a mozbase python test exits with "No test path ... contains". Python source-test jobs aren't covered, so the per-config failure rate took about 15 minutes of Treeherder API calls and log grepping.

- **Question:** How often does a python-test (source-test mozbase) test fail, per config?
- **Command:** `fx-tests test testing/mozbase/mozproxy/tests/test_proxy.py`
- **Expected:** Rates per config, or at least the failing source-test jobs naming the file.
- **Got:** "No test path in the xpcshell and mochitest 21-day data contains ..." (exit 2).
- **Example from:** [intermittent/testing/mozbase/mozproxy/tests/test_proxy.py.files/fx-tests-feedback.md](reports/intermittent/testing/mozbase/mozproxy/tests/test_proxy.py.files/fx-tests-feedback.md)

## 222. `intermittent --bug` drops "profile uploaded in" lines and the test they name (1)

`fx-tests intermittent --bug` ignored starred lines ending in "profile uploaded in …", and with them the only test path, so "Tests named" showed an unrelated test instead of the one behind all 4 occurrences.

- **Question:** Which test's failures are sheriffs actually starring on this bug?
- **Command:** `fx-tests intermittent --bug 1767905`
- **Expected:** "Tests named" to list `toolkit/mozapps/update/tests/unit_aus_update/languagePackUpdates.js` for all 4 occurrences.
- **Got:** "Tests named, per annotated job: 1x security/manager/ssl/tests/unit/test_faulty_server.js", from one job's secondary lines.
- **Suggestion:** Show a per-occurrence test path, and warn when the starred lines' tests don't match the test named in the bug summary.
- **Example from:** [intermittent/testing/awsy/awsy/test_memory_usage.py.bug1767905.files/fx-tests-feedback.md](reports/intermittent/testing/awsy/awsy/test_memory_usage.py.bug1767905.files/fx-tests-feedback.md)

## 223. `task` misses wpt variant failures whose URL has a `?query` suffix (1)

`fx-tests task` on a wpt job reported no failing test, though the log has a `TEST-UNEXPECTED-FAIL` for a `render-corner-shape.html?corner-shape=...` variant. Another job listed 12 failures but not the variant one.

- **Question:** Did this wpt job fail on the render-corner-shape variant?
- **Command:** `fx-tests task IimXDwALRwyG9cxS4IwInw`
- **Expected:** `TEST-UNEXPECTED-FAIL | /css/css-borders/corner-shape/render-corner-shape.html?corner-shape=-2&border-radius=20%&border-width=20` listed under FAILED.
- **Got:** "5795 tests, 5795 executions, 0 failing ... No test-level failure in this job."
- **Suggestion:** Match wpt test IDs including their `?query` variant suffix.
- **Example from:** [intermittent/testing/web-platform/tests/css/css-borders/corner-shape/render-corner-shape.html.bug2070424.files/fx-tests-feedback.md](reports/intermittent/testing/web-platform/tests/css/css-borders/corner-shape/render-corner-shape.html.bug2070424.files/fx-tests-feedback.md)

## 224. `intermittent --bug` shows each job's first failure, not the starred one (1)

`fx-tests intermittent --bug` "Failure messages" and "Tests named" showed each job's first failure, from a different test. It looked like sheriffs had starred unrelated failures, but both jobs did contain this bug's failure.

- **Question:** What failed in the jobs sheriffs starred on this bug?
- **Command:** `fx-tests intermittent --bug 2064429 --full-messages`
- **Expected:** The failure the bug is about (the "logged while in progress" ERROR for this test).
- **Got:** "Failure messages, per annotated job: 2x test_default_profile_does_not_exist - [...] 11 == 21" and "Tests named: test_backgroundupdate_exitcodes.js".
- **Suggestion:** Show the failure line matching the bug summary, or flag that listed messages are the job's first failure.
- **Example from:** [intermittent/toolkit/crashreporter/test/unit/test_crash_moz_crash.js.files/fx-tests-feedback.md](reports/intermittent/toolkit/crashreporter/test/unit/test_crash_moz_crash.js.files/fx-tests-feedback.md)

## 225. `test --config ... --history` day counts don't show which jobs they come from (1)

`fx-tests test <path> --config macosx1500 --history` gave per-day counts such as `2 pass 4 fail` that couldn't be mapped to jobs. Resource-usage profiles showed the test runs in all four weekly jobs, not one chunk of each.

- **Question:** Which pass counts belong to which weekly job?
- **Command:** `fx-tests test <path> --config macosx1500 --history`
- **Expected:** The job names behind each day's counts.
- **Got:** e.g. 2026-09-14 `2 pass 4 fail`, not mappable to jobs.
- **Suggestion:** Show the job names behind a day's counts.
- **Example from:** [mac/dom/media/webrtc/tests/mochitests/test_getUserMedia_basicScreenshare.html.files/fx-tests-feedback.md](reports/mac/dom/media/webrtc/tests/mochitests/test_getUserMedia_basicScreenshare.html.files/fx-tests-feedback.md)

## 226. Repeated `test --config a --config b` silently matches neither restriction (1)

`fx-tests test --history` with two `--config` flags gave counts matching neither restriction alone, with no warning or error.

- **Question:** What is this test's history on standalone windows11-64-25h2 jobs?
- **Command:** `fx-tests test <path> --history --config windows11-64-25h2 --config standalone`
- **Expected:** An AND of both, or an error for a repeated flag.
- **Got:** Counts matching neither restriction alone (217 runs on a day where standalone had 48), no warning.
- **Suggestion:** AND repeated `--config` values, or reject the repeated flag.
- **Example from:** [intermittent/browser/components/genai/tests/browser/browser_link_preview_telemetry.js.files/fx-tests-feedback.md](reports/intermittent/browser/components/genai/tests/browser/browser_link_preview_telemetry.js.files/fx-tests-feedback.md)

## 227. `test --task-ids --issue` text lacks FAIL-PARALLEL vs FAIL-SEQUENTIAL split per issue (1)

`fx-tests test --task-ids --issue N` doesn't give the parallel vs sequential split per issue, and prints the FAIL-SEQUENTIAL rows as an unlabelled second list. Here 74 of the test's 75 sequential failures were one mode, which the aggregate line hides.

- **Question:** How often does this failure mode fail in the parallel phase vs on the sequential retry?
- **Command:** `fx-tests test browser/components/newtab/test/xpcshell/test_nimbus_newtabTrainhopAddon.js --task-ids --limit 0 --issue 4`
- **Expected:** The FAIL-PARALLEL / FAIL-SEQUENTIAL split for the issue, or a labelled second list.
- **Got:** 320 task IDs by date, then a second run of date headers with 29 more and no heading; `--json` shows they are `FAIL-SEQUENTIAL`.
- **Suggestion:** Show the FAIL-PARALLEL / FAIL-SEQUENTIAL split per issue in text output, and label the sequential list.
- **Example from:** [browser/browser/components/newtab/test/xpcshell/test_nimbus_newtabTrainhopAddon.js.version-upgrades.files/fx-tests-feedback.md](reports/browser/browser/components/newtab/test/xpcshell/test_nimbus_newtabTrainhopAddon.js.version-upgrades.files/fx-tests-feedback.md)

## 228. `fx-tests test` Issues splits one shutdown leak into two failure modes (1)

`fx-tests test` Issues lists the two messages printed for one shutdown leak (browser-test.js and runtests.py's leak parser) as two separate failure modes.

- **Question:** What are the distinct failure modes of this test?
- **Command:** `fx-tests test <path>`
- **Expected:** One failure mode for the shutdown window leak.
- **Got:** `382x leaked window until shutdown [url = about:support]` and `271x leaked 1 window(s) until shutdown [url = about:support]`.
- **Suggestion:** Merge the two leak messages from the same shutdown into one Issues row.
- **Example from:** [mac/toolkit/content/tests/browser/browser_starting_autoscroll_in_about_content.js.files/fx-tests-feedback.md](reports/mac/toolkit/content/tests/browser/browser_starting_autoscroll_in_about_content.js.files/fx-tests-feedback.md)

## 229. `test` Issues don't flag that the top failure mode is tree-wide (1)

`fx-tests test` listed `CRASH child process hang at shutdown` as the top issue with no hint that it hits 507 tests, so it read as test-specific. The agent only found out from `fx-tests task` and `fx-tests crashes`.

- **Question:** Is this failure mode specific to my test, or tree-wide?
- **Command:** `fx-tests test devtools/server/tests/xpcshell/test_MemoryActor_saveHeapSnapshot_01.js`
- **Expected:** Next to the top issue (`CRASH child process hang at shutdown`, 111x), a hint that it hits many other tests on the same config.
- **Got:** Nothing; found only via `fx-tests task <id>` (95 tests crashed in one job) and `fx-tests crashes` (19,591 crashes across 507 tests).
- **Suggestion:** Flag Issues whose message or crash signature also hits many other tests.
- **Example from:** [devtools/devtools/server/tests/xpcshell/test_MemoryActor_saveHeapSnapshot_01.js.files/fx-tests-feedback.md](reports/devtools/devtools/server/tests/xpcshell/test_MemoryActor_saveHeapSnapshot_01.js.files/fx-tests-feedback.md)

## 230. xpcshell failure message lacks `[function : line]` suffix in `task --messages` (1)

`fx-tests task <id> --messages` showed an xpcshell failure as just `false == true`, without the `[<function> : <line>]` suffix the log line carries, so the failing assertion couldn't be located.

- **Question:** Which assertion in the test failed?
- **Command:** `fx-tests task DGJfOqsLS66aFU_vAV-6fA --messages`
- **Expected:** The message with its `[<function> : <line>]`, as other tests' messages in the same output have.
- **Got:** `false == true` only (others show e.g. `[test_mozillaonline_distribution_ignored : 72]`).
- **Suggestion:** Keep the `[<function> : <line>]` suffix in every xpcshell message.
- **Example from:** [devtools/devtools/server/tests/xpcshell/test_logpoint-03.js.files/fx-tests-feedback.md](reports/devtools/devtools/server/tests/xpcshell/test_logpoint-03.js.files/fx-tests-feedback.md)

## 231. `crash` doesn't flag unsymbolicatable modules; `test` splits them into ~90 Issues (1)

`fx-tests crash` showed only `xul.dll + 0x...` frames without saying the module has no debug id. 94 of 115 crashes were like that, each its own signature, so `fx-tests test` split one crash into ~90 Issues rows.

- **Question:** Are these unsymbolicated crashes the same stack as the symbolicated one?
- **Command:** `fx-tests crash fujONjImTxWcBoeLq-nz9Q.0 9d107df2-f9df-42ad-9fe5-d350f7554bd5`
- **Expected:** A symbolicated stack, or a hint why not.
- **Got:** `xul.dll + 0x...` frames only; `--raw` shows `debug_id` all zeros and `debug_file` empty.
- **Suggestion:** Say "module has no debug id: unsymbolicatable", and group such signatures in Issues as "xul.dll + <offset> (unsymbolicated, N distinct)".
- **Example from:** [intermittent/browser/components/tabbrowser/test/browser/tabs/browser_tab_preview.js.bug1980036.win32-crash.files/fx-tests-feedback.md](reports/intermittent/browser/components/tabbrowser/test/browser/tabs/browser_tab_preview.js.bug1980036.win32-crash.files/fx-tests-feedback.md)

## 232. `fx-tests task` records PASS when a fatal TEST-FAIL precedes the test's PASS (1)

`fx-tests task` left out a test that hit a `fatal condition` TEST-FAIL, because during dump-and-quit the test then logged its own TEST-PASS, which `task` kept.

- **Question:** Which tests failed in this job?
- **Command:** `fx-tests task ITGa2xW4Tm-I95lW9YpFsQ --profiles`
- **Expected:** `browser/components/urlbar/tests/browser-newtab/browser_selection.js` listed as failing (log has `TEST-FAIL | ... | fatal condition - Attempting to connect to non-local address!`).
- **Got:** Only other tests' failures; `--json` records browser_selection.js as `"statuses": ["PASS"], "executionCount": 2`.
- **Example from:** [intermittent/bugs/1534590.files/fx-tests-feedback.md](reports/intermittent/bugs/1534590.files/fx-tests-feedback.md)

## 233. `test --json` returns empty `configs` when the window has no failure (1)

`fx-tests test <path> --since 10 --json` returned `"configs": []` when the window had no failure, so per-config run counts were only available via `--coverage`, not the JSON.

- **Question:** What were the per-config runs and failures after the fix landed?
- **Command:** `fx-tests test <path> --since 10 --json`
- **Expected:** Per-config run counts in the JSON even with no failures.
- **Got:** `"configs": []`; only `--coverage` has per-config run counts.
- **Example from:** [mac/devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-sidebar-values-search.js.files/fx-tests-feedback.md](reports/mac/devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-sidebar-values-search.js.files/fx-tests-feedback.md)

## 234. `errors --message` shows a test count but no test names without `--group-by test` (1)

`fx-tests errors --message <text>` prints one aggregated row with occurrence and test counts but no test names, contrary to the guide ("Lists the tests emitting one message"). It needed `--group-by test`.

- **Question:** Which tests emit this message?
- **Command:** `fx-tests errors --message 'Component is not available"  nsresult: "0x'`
- **Expected:** Per the guide, the tests behind the row.
- **Got:** One aggregated row (33,439 occurrences, 36 tests) and no test names.
- **Suggestion:** List the tests by default with `--message`, or fix the guide to mention `--group-by test`.
- **Example from:** [browser/browser/base/content/test/browser-siteIdentity/browser_identityIcon_img_url.js.files/fx-tests-feedback.md](reports/browser/browser/base/content/test/browser-siteIdentity/browser_identityIcon_img_url.js.files/fx-tests-feedback.md)

## 235. `task --messages --full-messages` still replaces numbers with X (1)

`fx-tests task <id> --messages --full-messages` still applies the grouping normalization, turning "Test ran for 255s" into "Test ran for Xs" and erasing the one value needed for a single task.

- **Question:** How long did the failing runs take, against which limit?
- **Command:** `fx-tests task O02_k9H2QGSuG85fW4AcPw --messages --full-messages`
- **Expected:** `Test ran for 255s, limit was 180s (timeout factor 2).`
- **Got:** `Test ran for Xs, limit was 180s (timeout factor 2).`
- **Suggestion:** Show the raw message (with numbers) when `--full-messages` is given for one task.
- **Example from:** [browser/browser/components/aiwindow/ui/test/browser/browser_aiwindow_group_tabs_button.js.files/fx-tests-feedback.md](reports/browser/browser/components/aiwindow/ui/test/browser/browser_aiwindow_group_tabs_button.js.files/fx-tests-feedback.md)

## 236. `fx-tests crash` gives no memory-map summary for 32-bit OOM dumps (1)

For a 32-bit Windows OOM crash, `fx-tests crash` showed only signature, type, address and stack, so address-space fragmentation couldn't be told from exhaustion. The agent parsed the dumps' MemoryInfoListStream with a script.

- **Question:** Was the address space full, or fragmented?
- **Command:** `fx-tests crash <task> <minidump>`
- **Expected:** A summary of MemoryInfoListStream (free total, largest free block, reserved/committed by type), as Socorro does.
- **Got:** nothing about the memory map; the processed `.json` artifact has none either.
- **Suggestion:** Summarize MemoryInfoListStream; handle sign-extended 32-bit addresses and subtract the crash reporter's 80 MiB reservation.
- **Example from:** [browser/browser/components/aiwindow/ui/test/browser/browser_smartwindow_manage_tabs_tool.js.win32-crash.files/fx-tests-feedback.md](reports/browser/browser/components/aiwindow/ui/test/browser/browser_smartwindow_manage_tabs_tool.js.win32-crash.files/fx-tests-feedback.md)

## 237. No way to list sibling jobs of a push that ran the same manifest (1)

The failing config (linux tsan) uploads no per-test profiles. Finding the same manifest's job on another config of the same push took `label-to-taskid.json` plus 15 task-definition requests. No fx-tests command answers this.

- **Question:** Which job on the same push ran this manifest on another config?
- **Command:** *(no fx-tests command answers this)*
- **Expected:** A list of the push's sibling jobs that ran the manifest, with their per-test profile URLs.
- **Got:** No command; read each chunk's `MOZHARNESS_TEST_PATHS` from its task definition (15 requests).
- **Suggestion:** `fx-tests task <taskId> --same-push <config>`, listing sibling jobs that ran the manifest with their per-test profile URLs.
- **Example from:** [browser/browser/components/customizableui/test/browser_934113_menubar_removable.js.files/fx-tests-feedback.md](reports/browser/browser/components/customizableui/test/browser_934113_menubar_removable.js.files/fx-tests-feedback.md)

## 238. `test --task-ids` shows the minidump ID but not the crash signature (1)

For a test with 14x `@ mozalloc_handle_oom` and 7 distinct `@ xul.dll + 0x...` crashes, `fx-tests test <path> --task-ids` didn't say which task had which signature, so the agent ran `fx-tests crash` on 9 tasks one by one.

- **Question:** Which crash signature did each failing task have?
- **Command:** `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_tab_switching.js --task-ids --limit 0`
- **Expected:** Next to each CRASH task, its signature, to pick the unsymbolicated ones.
- **Got:** Task ID, job name and minidump ID only; `--json` `taskIds[]` has no `signature` field either.
- **Suggestion:** Add the crash signature per CRASH task in text and a `signature` field in JSON.
- **Example from:** [intermittent/browser/components/aiwindow/ui/test/browser/browser_smartwindow_tab_switching.js.bug2027597.files/fx-tests-feedback.md](reports/intermittent/browser/components/aiwindow/ui/test/browser/browser_smartwindow_tab_switching.js.bug2027597.files/fx-tests-feedback.md)

## 239. Crash after a run's first failure missing from `crashes` and `test` (1)

A `RunWatchdog` shutdown-hang crash that followed a run's first failure appeared in neither `fx-tests crashes` nor `fx-tests test`'s crash count. The run was counted only as a FAIL.

- **Question:** How often does this crash signature happen tree-wide?
- **Command:** `fx-tests crashes --harness mochitest --limit 0 | grep RunWatchdog`
- **Expected:** A row for `@ mozilla::(anonymous namespace)::RunWatchdog`, which this test hit on 2026-09-18, inside the window.
- **Got:** No row. `fx-tests test <path>` also reports `0 crash` for the test over the window.
- **Example from:** [devtools/devtools/client/aboutdebugging/test/browser/browser_aboutdebugging_thisfirefox.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/aboutdebugging/test/browser/browser_aboutdebugging_thisfirefox.js.files/fx-tests-feedback.md)

## 240. `fx-tests try` counts two chunks of one config as "2 configs" (1)

`fx-tests try` summarized test_arrowpanel.xhtml as failing "on 2 configs", but they were two chunks of one config. The same entry also left out 5 failing jobs killed at max run time, so the test seemed to pass on opt.

- **Question:** On which jobs of the push did `test_arrowpanel.xhtml` fail?
- **Command:** `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids --limit 0`
- **Expected:** All 8 macosx1500-aarch64 `mochitest-chrome` jobs, opt and debug, with configs counted by platform/build type.
- **Got:** "6 failures in 7 runs, across 4 job runs on 2 configs", naming only the two debug chunks.
- **Suggestion:** Count configs by platform/build type rather than by chunk.
- **Example from:** [mac/toolkit/content/tests/chrome/test_arrowpanel.xhtml.files/fx-tests-feedback.md](reports/mac/toolkit/content/tests/chrome/test_arrowpanel.xhtml.files/fx-tests-feedback.md)

## 241. `fx-tests try <autoland rev>` says no push found instead of detecting the repo (1)

`fx-tests try` given an autoland revision answers "no push found for revision on try". It doesn't detect the repo or point to `--project`, so the agent couldn't easily get the "all failures in this push" view.

- **Question:** Which push made this one-day spike, and what failed in it?
- **Command:** `fx-tests try 82333bc598c6`
- **Expected:** The same "all failures in this push" view for the autoland revision.
- **Got:** "no push found for revision on try".
- **Suggestion:** Detect the revision's repo, or suggest `--project autoland`.
- **Example from:** [devtools/devtools/client/webconsole/test/browser/browser_webconsole_stacktrace_mapped_location_debugger_link.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/webconsole/test/browser/browser_webconsole_stacktrace_mapped_location_debugger_link.js.files/fx-tests-feedback.md)

## 242. `test --task-ids` rows lack each job's failure statuses (1)

`fx-tests test <path> --task-ids` lists only task and config. The per-job status (`CRASH, TIMEOUT` vs `TIMEOUT`), which told the leaking runs apart, appears only in `fx-tests task`, so the agent ran 17 `task` calls.

- **Question:** In the jobs where test A failed with status X, did test B run, and did it pass?
- **Command:** `fx-tests test toolkit/crashreporter/test/unit/test_crashreporter_crash.js --task-ids --limit 0`
- **Expected:** Each job's failure status (e.g. `CRASH, TIMEOUT` vs `TIMEOUT`) and whether a second test ran in the same job.
- **Got:** Only task and config; the status (`CRASH, TIMEOUT — 2 failing executions of 3`) only appears in `fx-tests task`.
- **Suggestion:** Show per-job statuses in `--task-ids` rows, and e.g. `--with <B>` printing B's outcome in each job.
- **Example from:** [intermittent/toolkit/components/backgroundtasks/tests/xpcshell/test_backgroundtask_shouldprocessupdates.js.files/fx-tests-feedback.md](reports/intermittent/toolkit/components/backgroundtasks/tests/xpcshell/test_backgroundtask_shouldprocessupdates.js.files/fx-tests-feedback.md)

## 243. `issues` refuses `--config`, can't rank tests failing on one platform (1)

`fx-tests issues` refuses `--config`, giving tree-wide totals only, so ranking tests failing on Mac 15.30 needed `fx-tests test --coverage --json` on each of 6,200 tests.

- **Question:** Which tests failed on one platform (Mac 15.30), compared with the others?
- **Command:** `fx-tests issues --harness <h> --type fail --type timeout --type crash --group-by test --limit 0`
- **Expected:** A way to rank tests failing on one platform or job-name substring, with their rate there and elsewhere.
- **Got:** Tree-wide totals only (the documented `canAttributeConfigs: false`), so 5,850 tests had to be run one by one.
- **Suggestion:** `issues --config macosx1500` using the per-test bucket data `test` already reads, with a column for other configs' rate.
- **Example from:** [mac/todo.files/fx-tests-feedback.md](reports/mac/todo.files/fx-tests-feedback.md)

## 244. `skips --json` has empty `jobNames`, so skips can't be attributed to configs (1)

`fx-tests skips --json` has `jobNames: []` on every row, so which platforms a skip applied to could only be guessed from the condition text, with counts covering all platforms together.

- **Question:** Which skipped tests are skipped on one platform (Mac 15.30)?
- **Command:** `fx-tests skips --harness <h> --limit 0 --json`
- **Expected:** Which configs each skip applied to.
- **Got:** `jobNames` is `[]` on every row; the platform only comes from the condition text in `messages`, whose counts cover every platform together.
- **Suggestion:** Fill `jobNames` in `skips`, using the per-config `skipCount`/`skipMessages` that `test --coverage --json` already has.
- **Example from:** [mac/todo.files/fx-tests-feedback.md](reports/mac/todo.files/fx-tests-feedback.md)

## 245. `fx-tests try --harness` doesn't filter rows; help doesn't say so (1)

`fx-tests try --harness xpcshell` returned the same mixed mochitest and xpcshell rows as `--harness mochitest`; the flag seems to change only the central comparison, and the help doesn't say so.

- **Question:** Which xpcshell tests failed on this try push?
- **Command:** `fx-tests try <rev> --harness xpcshell --json`
- **Expected:** xpcshell failures only.
- **Got:** The same 133 rows, mochitest and xpcshell mixed, for both `--harness xpcshell` and `--harness mochitest`.
- **Suggestion:** Document in the help that `--harness` only changes the central history comparison.
- **Example from:** [mac/todo.files/fx-tests-feedback.md](reports/mac/todo.files/fx-tests-feedback.md)

## 246. `task --messages` Nx counts distinct messages, not how often each was logged (1)

`fx-tests task --messages` showed `1x` for a message logged three times (once per `add_task`), which the agent misread as the later subtests passing.

- **Question:** Did only the first task of browser_878452 fail, or all three?
- **Command:** `fx-tests task ZnpEvNYJR265Mcda_YWPIw.1 --profiles --messages`
- **Expected:** The count in front of each message to be how many times it was logged in the job.
- **Got:** `1x Uncaught exception in test - TypeError: can't access property "dataTransfer", sess is null`, while the test's profile shows it three times.
- **Suggestion:** Make the Nx count the number of times the message was logged.
- **Example from:** [browser/browser/components/customizableui/test/browser_884402_customize_from_overflow.js.files/fx-tests-feedback.md](reports/browser/browser/components/customizableui/test/browser_884402_customize_from_overflow.js.files/fx-tests-feedback.md)

## 247. No manifest-neighbours view of fail rates in manifest order (1)

To spot a cluster of failing tests in one manifest on one config, the agent had to run `fx-tests test` on each neighbouring test in a shell loop (9 tests). No view lists a manifest's tests with fail rate and first message.

- **Question:** Which tests in this manifest fail together on this config?
- **Command:** *(no fx-tests command answers this)*
- **Expected:** For one config, each test's fail rate and first message, in manifest order.
- **Got:** No such view; `fx-tests test <each neighbouring test>` run in a shell loop over 9 tests.
- **Suggestion:** A manifest-level (or `--neighbours`) view listing each test's fail rate and first message for one config, in manifest order.
- **Example from:** [browser/browser/components/customizableui/test/browser_885530_showInPrivateBrowsing.js.files/fx-tests-feedback.md](reports/browser/browser/components/customizableui/test/browser_885530_showInPrivateBrowsing.js.files/fx-tests-feedback.md)

## 248. `-cf` confirm-failure jobs drive a "perma-fail" verdict (1)

`fx-tests test` reported a perma-fail based only on a `-cf` job, which runs only after a failure so fails by construction, even though all failures came from one push/day and it had stopped failing.

- **Question:** Is this test still failing, or was it confined to one push?
- **Command:** `fx-tests test dom/canvas/test/webgl-conf/generated/test_conformance__canvas__draw-static-webgl-to-multiple-canvas-test.html`
- **Expected:** A headline that all 9 failures are from one day (2026-09-14) and one push, i.e. "stopped failing".
- **Got:** `Verdict: perma-fail. Never passed on 1 configuration: ...-nofis-cf (5/5)`.
- **Suggestion:** Leave `-cf` jobs out of the perma-fail verdict or label them; say when all failures are on one day/push with passes after.
- **Example from:** [intermittent/dom/canvas/test/webgl-conf/generated/test_conformance__canvas__draw-static-webgl-to-multiple-canvas-test.html.files/fx-tests-feedback.md](reports/intermittent/dom/canvas/test/webgl-conf/generated/test_conformance__canvas__draw-static-webgl-to-multiple-canvas-test.html.files/fx-tests-feedback.md)

## 249. `test --history` shows a harness attribution change as an unexplained regression step (1)

`fx-tests test <path> --history` showed 0 failures until 2026-09-13, then timeouts from 09-14. That matched bug 2062615 newly attributing Android no-output timeouts to tests, not a regression; nothing in the output said so.

- **Question:** When did this test start failing?
- **Command:** `fx-tests test dom/media/autoplay/test/mochitest/test_autoplay_gvobserver_late_auth.html --history`
- **Expected:** Failures on every weekday; Treeherder shows 20-37 annotated failures per weekday from 2026-08-17 to 2026-09-13.
- **Got:** 0 fail / 0 timeout every day 2026-09-01 .. 2026-09-13, then 10/27/7 timeouts on 09-14/15/16.
- **Suggestion:** Note harness-attribution changes inside the window, or show per-day annotated failure counts of the test's bugs alongside.
- **Example from:** [intermittent/dom/media/autoplay/test/mochitest/test_autoplay_gvobserver_late_auth.html.files/fx-tests-feedback.md](reports/intermittent/dom/media/autoplay/test/mochitest/test_autoplay_gvobserver_late_auth.html.files/fx-tests-feedback.md)

## 250. `intermittent --harness unknown` returns `test: null` for resolvable test files (1)

`fx-tests intermittent --harness unknown` gave `test: null` on all 699 rows. 402 of them name a test file in the tree (WPT, reftest, junit, marionette, jit-test), which had to be resolved by a script.

- **Question:** Which test file does each `unknown`-harness bug name?
- **Command:** `fx-tests intermittent --harness unknown --since 21 --limit 0 --json`
- **Expected:** A path for bugs whose summary names a test of a suite fx-tests does not read.
- **Got:** `test: null` on all 699 rows, though 402 name a test file in the tree.
- **Suggestion:** Resolve the test column against the tree for any suite, and show the suite name.
- **Example from:** [intermittent/todo.files/fx-tests-feedback.md](reports/intermittent/todo.files/fx-tests-feedback.md)

## 251. `intermittent` text/markdown output shows no bug status or restricted-bug note (1)

`fx-tests intermittent` text and `--markdown` output has no status column, so closed bugs can't be spotted. `status`/`resolution` are only in `--json`, and two restricted bugs come with `bugSummary: null` and no explanation.

- **Question:** Which of these bugs are closed?
- **Command:** `fx-tests intermittent --harness mochitest --since 21 --limit 0`
- **Expected:** Bug status visible in the list.
- **Got:** No status column; `status`/`resolution` only in `--json`. Restricted bugs 2074018, 2071379 have `bugSummary: null` and nothing saying why.
- **Suggestion:** Show `(FIXED)` next to closed bugs, and "restricted" for bugs whose summary Bugzilla withholds.
- **Example from:** [intermittent/todo.files/fx-tests-feedback.md](reports/intermittent/todo.files/fx-tests-feedback.md)

## 252. `fx-tests test` Issues rows don't show which bug each mode was starred on (1)

`fx-tests test` lists a test's failure modes and `fx-tests intermittent --bug N` lists a bug's messages, but nothing maps one to the other.

- **Question:** Which bug does each failure mode of this test belong to?
- **Command:** `fx-tests test <path>`
- **Expected:** The bug sheriffs starred each issue on, next to the issue under Issues.
- **Got:** Issues (FAIL `1 === null`, TIMEOUT) with no bug mapping.
- **Suggestion:** Show the bug each issue was starred on next to it under Issues.
- **Example from:** [intermittent/bugs/2064983.files/fx-tests-feedback.md](reports/intermittent/bugs/2064983.files/fx-tests-feedback.md)

## 253. `task` "named no test path" warning omits the crash signature (1)

`fx-tests task` warned about a CRASH marker naming only the manifest, not a test, but showed no signature, and `--messages`/`--json` lacked it too. Finding which crash followed the test's TIMEOUT took 7 profile loads.

- **Question:** Which process crash followed my test's TIMEOUT in this job, and with what signature?
- **Command:** `fx-tests task L_hUPhc6RRyZ_Ak5F4JCAA --profiles`
- **Expected:** The crash signature (and time) of the manifest-attributed crash.
- **Got:** `warning: 1 failing marker in this job named no test path and is not in the table below ... CRASH toolkit/components/ml/tests/browser/browser.toml`, no signature.
- **Suggestion:** Print the signature and time in the warning, e.g. `CRASH @ <signature> — browser.toml, 92 s after browser_ml_utf8_codepoint.js TIMEOUT`.
- **Example from:** [mac/toolkit/components/ml/tests/browser/browser_ml_utf8_codepoint.js.files/fx-tests-feedback.md](reports/mac/toolkit/components/ml/tests/browser/browser_ml_utf8_codepoint.js.files/fx-tests-feedback.md)

## 254. `fx-tests try --test` reports passed despite job-failing ASan SEGV line (1)

`fx-tests try --test` reported the test as passed on every config. But each ASAN run emitted `ERROR: AddressSanitizer: SEGV` during the test's span, which turns the job red.

- **Question:** Did this test turn the job red even though it passed?
- **Command:** `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/framework/browser-toolbox/test/browser_browser_toolbox_debugger.js`
- **Expected:** Some sign that each ASAN run emitted a job-failing error line (`ERROR: AddressSanitizer: SEGV ...`).
- **Got:** 21/21 "passed", 0 failed, on every config including both ASAN ones.
- **Suggestion:** Add a per-test "error lines emitted during this test's span" flag in `try --test` and `task`.
- **Example from:** [devtools/devtools/client/framework/browser-toolbox/test/browser_browser_toolbox_debugger.js.files/fx-tests-feedback.md](reports/devtools/devtools/client/framework/browser-toolbox/test/browser_browser_toolbox_debugger.js.files/fx-tests-feedback.md)

## 255. `test <dir> --config` refused, so a directory's failing tests on a config can't be listed (1)

To find any per-test profile from a given config's session of a manifest, the agent tried `fx-tests test <dir> --config ...`, which refuses directories, and `failures --config`, also refused. It looped over ~120 tests instead.

- **Question:** Which jobs on config X failed a test in this directory, with task IDs?
- **Command:** `fx-tests test browser/components/tabbrowser/test/browser/tabs --config windows11-64-25h2/debug-mochitest-browser-chrome-vt,...`
- **Expected:** The failing tests of that directory on those configs, with task IDs.
- **Got:** `test` refuses a directory ("is not a test path, and 198 tests match it"); `failures` refuses `--config`.
- **Suggestion:** Support `fx-tests test <dir> --config <cfg> --task-ids` listing task IDs per failing test in the directory.
- **Example from:** [mac/browser/components/tabbrowser/test/browser/tabs/browser_reload_deleted_file.js.files/fx-tests-feedback.md](reports/mac/browser/components/tabbrowser/test/browser/tabs/browser_reload_deleted_file.js.files/fx-tests-feedback.md)

## 256. `errors --message` matches message text only, not the `file:line` it prints (1)

`fx-tests errors --message "nsAHttpConnection.cpp:57"` found nothing, though the same warning is found by its text and then printed with that `file:line`. No hint that only message text is searched.

- **Question:** Which rows are there for the C++ warning at `nsAHttpConnection.cpp:57`?
- **Command:** `fx-tests errors --harness xpcshell --message "nsAHttpConnection.cpp:57"`
- **Expected:** Rows for the warning raised at `netwerk/protocol/http/nsAHttpConnection.cpp:57`.
- **Got:** `No markers matched.`; `--message "sts->Dispatch(event.forget()"` finds it and prints that file:line under "Where they come from".
- **Suggestion:** Let `--message` also match `file:line`, or say in the no-match output that only message text is searched.
- **Example from:** [mac/netwerk/test/unit/test_webtransport_stop_sending.js.nss-shutdown-crash.files/fx-tests-feedback.md](reports/mac/netwerk/test/unit/test_webtransport_stop_sending.js.nss-shutdown-crash.files/fx-tests-feedback.md)

## 257. `errors --json` gives counts per test but no task IDs (1)

`fx-tests errors` reported "3 occurrences in test_webtransport_stop_sending.js" but no task IDs, in text or JSON, so they couldn't be checked against the 2 crash jobs `test --task-ids` lists.

- **Question:** In which jobs did this test print this warning?
- **Command:** `fx-tests errors --harness xpcshell --day 2026-09-19 --message "sts->Dispatch(event.forget()" --json`
- **Expected:** The task IDs behind the 3 occurrences in test_webtransport_stop_sending.js.
- **Got:** Counts per test only. The JSON has no task IDs either.
- **Suggestion:** Give task IDs (or `--task-ids`) per test row.
- **Example from:** [mac/netwerk/test/unit/test_webtransport_stop_sending.js.nss-shutdown-crash.files/fx-tests-feedback.md](reports/mac/netwerk/test/unit/test_webtransport_stop_sending.js.nss-shutdown-crash.files/fx-tests-feedback.md)
