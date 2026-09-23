# fx-tests feedback (browser_console_restore.js)

## Did the try push schedule the job type that the removed `skip-if` condition targets?

- Command: `fx-tests try 2888bcab0070 --harness mochitest --test <path> --all-jobs`
- Expected: when a test passed everywhere, some line saying whether the push ran the jobs that its removed condition covers. Here the condition was `verify`, so that means test-verify (TV) jobs. Failing that, a list of the job types the push scheduled.
- Got: only the configs that ran the test. Their absence cannot be told apart from "not scheduled".
- Workaround: I scripted over the Treeherder API (`/api/project/try/push/?revision=`, then `/api/jobs/?push_id=`) and grepped the job type names for `verify`.

## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test devtools/client/webconsole/test/browser/browser_console_restore.js --harness mochitest --bugs`
- Expected: a "Bugs" section, or a line saying "no open bug names this test".
- Got: the same output as without `--bugs`, so I could not tell whether the flag had run.
- Workaround: none; I read the manifest history instead.

## Job count includes Taskcluster `retry` runs, without saying so

- Command: `fx-tests try 2888bcab0070 --harness mochitest --test <path>` (without `--all-jobs`)
- Expected: the same job count as with `--all-jobs`, or a note that some runs were infra retries.
- Got: `jobs 4` for linux opt chunk 5 and a11y-checks chunk 5, but 3 with `--all-jobs`. The fourth run was a Taskcluster `retry` result, an infra rerun that never ran the test. I had to check that on Treeherder.
