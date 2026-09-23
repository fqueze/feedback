## Question: which failure message did each job sheriffs starred on this bug actually have?

- Command: `fx-tests intermittent --bug 2070405 --since 30 --tree all --limit 0`
- Expected: per occurrence, the error line the job failed with (or at least a grouping by message), since sheriffs star unrelated failures on perma bugs.
- Got: job name, task id, push time only; "Tests named" is empty because perftest logs carry no TEST-UNEXPECTED-FAIL line.
- Workaround: downloaded all 57 `live_backing.log` files and grepped them. 43 were the bug's KeyError, 8 were the sibling KeyError of bug 2070360, 4 were `InvalidLastFrame` (bug 2070794), 1 a strptime ValueError (bug 2072522), 1 a missing `TotalTime:` line. The output could show the last `Error:`/`ScriptFailedError:` line per occurrence for non-mochitest/xpcshell jobs.

## Question: when did this perftest job stop failing?

- Command: `fx-tests test testing/performance/mobile-startup/android_startup_cmff_cvns.py` (and `--history`)
- Expected: pass/fail per day for the perftest jobs that run it.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...": perftests are outside the data.
- Workaround: Treeherder REST `api/project/<repo>/jobs/?job_type_name=<job>&push_id__gte=..&push_id__lte=..` to get results per push. With a bug whose only jobs are perftests, `intermittent --bug` could say up front that `test`/`--history` cannot cover them.
