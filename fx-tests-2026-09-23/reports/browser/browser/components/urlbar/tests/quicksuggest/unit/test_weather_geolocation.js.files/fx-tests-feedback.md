## `fx-tests test <path> --bugs` prints no bug section when there is none

- Command: `fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_weather_geolocation.js --bugs`
- Expected: a "Bugs" section, saying "none found" if so.
- Got: exactly the default output, so it is unclear whether `--bugs` ran and found nothing.
- Workaround: `fx-tests intermittent --test <path> --since 21` (which does say no bug names the test) and Bugzilla REST searches.

## Question: "is this job-wide? how many tests in this job failed with the same message?"

- Command: `fx-tests task LEvE_LI7R_OKi-V60yZP_A --profiles`
- Expected: a grouping of the failing tests by message, e.g. "54 tests: CRASH child process hang at shutdown".
- Got: one block per test, truncated at 20 (`… 39 more`), so the scale (54 of 59 failures are the same crash) needs `--limit 0 | rg -c`.
- Workaround: `--limit 0` and counting with rg.

## Question: "in which process and where is each of a job's hang dumps stuck?"

- Command: `fx-tests crash <task> <dump> --all-threads` once per dump (54 dumps).
- Expected: some way to summarize many dumps of one job at once (process executable name, crashing-thread top frames under breakpad).
- Got: one dump per call; the process type (here "Firefox Nightly GPU Helper") is only visible as a bottom frame of the main thread.
- Workaround: shell loop over dump IDs extracted from the live log with rg, grepping each output.
