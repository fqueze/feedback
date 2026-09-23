## `test --bugs` prints nothing when no bug is found

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_nontab_process_switch.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names the test.
- Got: the same output as without `--bugs`. `--json` has `annotatedBugs: []`, so "none found" and "flag ignored" look the same.
- Workaround: a Bugzilla quicksearch with curl.

## Question: "in the jobs where the leaker failed, which tests after it did not fail, and why"

- Commands: `fx-tests test <leaker> --task-ids --limit 0` and `fx-tests test <this test> --task-ids --limit 0`, compared with `comm`. Then `fx-tests task <id> --limit 0` for each job only in the leaker's list.
- Expected: some way to get "jobs where A failed and B did not".
- Got: two lists to diff by hand. `fx-tests task` shows the `TIMEOUT` of `browser_ext_incognito_views.js`. It does not say that the harness restarted the browser after it, and that restart is why the later tests passed. The resource-usage profile shows it ("runtests.py | Application ran for", then a new "Application pid").
- Workaround: diff the lists, then load the resource-usage profile. `fx-tests task` could mark where the browser restarted within a manifest.

## Data window moved while I was working

- `fx-tests test` first answered for 2026-08-31..09-20, generated 2026-09-21. Later calls answered for 2026-09-01..09-21, generated 2026-09-22. The second list of failing jobs no longer had the 2026-08-31 jobs, and at first that looked like 2 jobs where this test failed without the leaker. The header does say which window it is, but a note when two calls in one session read different generations would have saved a detour.
