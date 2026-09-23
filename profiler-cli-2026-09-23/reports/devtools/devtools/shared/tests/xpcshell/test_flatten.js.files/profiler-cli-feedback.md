## Question: which tests were running at time t (and how many at once) in a resource-usage profile

- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json` then a Python script computing overlap with t=44.918 and concurrency per second.
- The default output could have answered with a "markers overlapping t" filter (e.g. `--at 44.918` or `--overlapping 44.9,45.0`), and a concurrency sparkline for interval markers of one name. With 1742 `test` markers, 20 of them long-running, reading start+duration off the list by eye to find the ones spanning a point is not practical.

## Question: which tests started but never got a `test_end` (review-test_flatten.js)

- Command: `profiler-cli thread markers --session review-test_flatten.js-1 --list --limit 0 --json`, then a Python script matching each `INFO "<test> failed or timed out, will retry."` to a `test` marker of the same test name.
- Expected: some way to see tests with a `test_start` but no `test_end`. Got: they have no `test` marker at all, so 279 of 1,075 failing tests in ZtblArCLSx6O6KzfEkL1YQ (xpcshell-remote.toml, 90 s kill timer that outlived the job) are invisible except through their INFO line. That made the concurrency count dip (11 open at 65 s) look like fewer running tests. An open-ended `test` marker (to the end of the profile) for unfinished tests, or a "join markers by field `test`" view, would have shown it.
