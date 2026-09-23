## Question: "which per-test Gecko profile covers this test's run" (a shutdown leak uploads none)

- Commands: `fx-tests test <path> --task-ids --limit 0`, then a shell loop of `fx-tests task <id> --profiles --quiet` over 40 failing swr tasks, grepping for `profile_*.json` URLs of other tests.
- Answer: per-test profiles of tests that run later in the same manifest and browser (browser_aiwindow_monitor_button.js, browser_aiwindow_smartbar_suggestions.js) contain this test's whole run, including its DOMEvent markers. That was the decisive evidence, since the failing test itself never gets a profile for a shutdown leak.
- What would have answered it: `fx-tests test <path> --profiles` could list, for failing jobs without a profile of their own, the per-test profiles of later tests in the same manifest and browser session (first run or `-2` retry), marked "covers this test's run".
