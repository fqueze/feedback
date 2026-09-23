## `fx-tests test <path> --bugs` prints no bug section when no bug matches

- Command: `fx-tests test browser/components/urlbar/tests/browser-engagementTelemetry/browser_glean_telemetry_keyword_exposure.js --bugs`
- Expected: a "Bugs" section, saying "none found" when no bug names the test.
- Got: the same output as without `--bugs`, with no bug section and no line saying the search ran. I could not tell "no bug" apart from "the flag was ignored".
- Workaround: searched Bugzilla directly (`/rest/bug?quicksearch=<file name>` and `?summary=keyword_exposure`).

## The question "which Firefox stdout lines did this test's browser print" had no direct command

- Question: did `[ERROR glean_core] Glean should not be initialized multiple times` appear in this job, and for which tests? It turned out to be the log signature of the failure.
- `fx-tests errors --message "Glean should not be initialized multiple times"` answered "No markers matched": the errors data does not include Rust `log` output lines (`output` markers).
- Workaround: loaded each job's resource-usage profile and ran `profiler-cli thread markers --search "Glean should not be initialized"`. That works, but needs one profile load per job.
- What could have shown it: `fx-tests errors` covering Firefox stdout lines such as `[ERROR <rust crate>]`, or `fx-tests task <id> --grep <text>` over the job's log.
