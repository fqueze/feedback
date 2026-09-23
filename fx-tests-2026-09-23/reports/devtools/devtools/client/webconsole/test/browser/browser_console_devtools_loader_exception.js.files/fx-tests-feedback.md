## Question: which tasks ran this (passing) test on a try push?

Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_console_devtools_loader_exception.js --task-ids`

Expected: the per-config table plus the task IDs of the jobs that ran the test, so I can open
the job (`fx-tests task`) or its resource-usage profile and check what else happened in it
(e.g. a shutdown leak not attributed to the test).

Got: the same per-config table as without `--task-ids`; `--task-ids` is silently ignored with
`--test`.

Workaround: grep the task IDs out of the default `fx-tests try --task-ids --limit 0` output,
which only lists them for jobs where some other test failed, and truncates at 5 per row
("… 37 more tasks"), so I had to go to `--json` for the Windows ones.

## Question: did this job have a manifest-level failure (a LeakSanitizer leak at shutdown)?

Command: `fx-tests task SG5h3qzRTDi60GlyHb25Sg --messages`

Expected: the job's `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at XPCWrappedNative::GetNewOrUsed, ... | devtools/client/webconsole/test/browser/_browser_console.toml`
listed, attributed to the manifest. That leak is exactly what the manifest's `skip-if = ["asan"]`
(Bug 1910261) was for, so it is the answer to "does removing that skip-if bring the failure back".

Got: "189 tests, ... 2 failing" and only the two per-test failures; nothing about the 9
LeakSanitizer TEST-UNEXPECTED-FAIL lines. `fx-tests try --all-jobs --test <path>` likewise reports
the test as 3/3 passed on test-linux2404-64-asan/opt-mochitest-devtools-chrome-2.

Workaround: loaded the resource-usage profile and ran
`profiler-cli thread markers --search LeakSanitizer --list`.
