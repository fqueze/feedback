## Question: which manifest(s) did each annotated zero-test job run? (bug 1989470, "No checks run")

- Command: `fx-tests intermittent --bug 1989470 --tree all --since 120 --limit 0`, then `fx-tests task buKLHEaDTw2GNRi9ufnUHg --profiles`
- Expected: for a bug whose jobs ran no test, the manifest(s) each job was given (`MOZHARNESS_TEST_PATHS` in the task definition), and the log's `The following path(s) didn't resolve any tests:` lines, so that occurrences can be grouped by manifest.
- Got: `intermittent` lists task ids only ("Tests named: none"); `task` says "This profile records no tests at all ... the log on Treeherder is the next step".
- Workaround: `--json`, then a script fetching every task definition from the Taskcluster queue and reading `payload.env.MOZHARNESS_TEST_PATHS`, then grepping each live_backing.log. It split the bug's 36 annotations into 5 distinct causes (27 places/unit, 4 qrcode, 3 nsis, 1 services/sync, 1 adb harness crash) that the default output could not separate.
- Also useful in the same table: the task's treeherder symbol (e.g. `X-85a6d625fc9-bk`), which says an occurrence is a sheriff backfill/retrigger of another push's task rather than an independent failure; 18 of the 22 were backfills.
