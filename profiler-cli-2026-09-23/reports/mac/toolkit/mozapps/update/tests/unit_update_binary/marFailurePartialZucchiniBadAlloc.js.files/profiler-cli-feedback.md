## `load` returns an error while the daemon is still starting, and the next commands fail too

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/XUny2WvoQ7-IzJFQvbY9yA/runs/0/artifacts/public/test_info/profile_resource-usage.json --session zbadalloc-2`, first load of a scripted loop over 27 resource-usage profiles, while other agents' daemons were running on the machine.
- Expected: `load` waits until the profile is loaded (or fails for a real reason), so the next `thread markers` command in the script can run.
- Got: `Error: The profiler-cli daemon did not become ready within 500ms, without writing anything to …/zbadalloc-2.log. It has not exited, so it is most likely still starting, and retrying often works.` Then `thread markers` gave `Error: Profile still loading, try again shortly`, twice. The script recorded an empty scan for that job. The other 26 loads in the same loop worked.
- Workaround: rerun that one job afterwards. A longer, or configurable, readiness wait in `load` (or a `--wait` flag that blocks until the profile is loaded) would make scripted loops safe.

## Question: in what order did log lines from parallel tests happen, within one second?

- Command: `profiler-cli thread markers --session zbadalloc-1 --search "marFailurePartialZucchiniBadAlloc.js,ACCESS_DENIED" --list --limit 0`
- Question: did this test's `NS_ERROR_FILE_ACCESS_DENIED` come before or after `marFailurePartial.js`'s, and how long after another test's updater launch? The three events are 13-57 ms apart.
- Got: every row reads `t=6m20s`, so the text output cannot order them or give the gaps. Rows are sorted, but same-time rows are indistinguishable, and a zoom does not change the resolution.
- Workaround: `--json` and a Python one-liner printing `start/1000` to the millisecond.
- What could have shown it: the `--list` time column at ms resolution (`t=379.898s`) when the view is short or when rows share a second. Or a `--time-format seconds` / `--precise` flag.

## Question: which mode is each of a test's 27 failing jobs in?

- Command: a shell loop over 27 resource-usage profiles, running `load`, then `thread markers --search "<test>.js" --list --limit 0`, then `stop`, and grepping the text for `Callback log does not exist`, `ACCESS_DENIED` and `launching the program`. About 15 minutes (`scan.sh` in this directory).
- What could have shown it: a batch mode, for example `profiler-cli markers-count --search A --search B <url>...`, that prints per-profile match counts for several searches without keeping a daemon per profile.
