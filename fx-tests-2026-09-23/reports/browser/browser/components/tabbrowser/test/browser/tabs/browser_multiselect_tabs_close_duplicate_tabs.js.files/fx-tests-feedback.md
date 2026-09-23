## Question: "does another test always fail before this one in the same job?"

- Command: `fx-tests test <path> --task-ids --limit 0`, then one `fx-tests task <id>` per failing job (81 calls in a shell loop, grepping for `browser_ctrlTab.js`).
- Expected: a way to see, across a test's failing jobs, which other tests failed in the same job (e.g. `fx-tests test <path> --co-failures`: "browser_ctrlTab.js failed first in 81/81 of these jobs").
- Got: only per-job answers from `fx-tests task`; the aggregate needed a loop of 81 calls (several minutes).
- Workaround: the loop. This co-failure count was the key evidence for an order dependency here (the earlier test's timeout restarts the browser).
## Question (review): "is there a profile of a passing run of this test?"

- Command: none answers it. I guessed from a sibling report: a later test in the same manifest (browser_multiselect_tabs_unload_telemetry.js) failed in job CHZDjjiuQJqhXjvVR7tCyQ, so its per-test profile covered this test passing in the same browser. I then checked with `profiler-cli thread markers --category Test --list --limit 0 | grep "  test  "`. A first guess (the splitview profile of IQozhc5QTo--odFN_5IcUg) had wrapped its buffer before this test ran.
- Expected: something like `fx-tests test <path> --passing-profiles`, listing per-test profiles of other tests that failed later in the same browser session, with how far back each reaches.
- Got: nothing. Two profile loads, one of them wasted.
