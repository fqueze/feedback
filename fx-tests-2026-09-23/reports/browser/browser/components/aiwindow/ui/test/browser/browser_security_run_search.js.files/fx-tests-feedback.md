## Issues list names fallout as the first failure

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_security_run_search.js`
- Expected: one failure mode, `Uncaught exception in test - [MockEngineManager] Failed to find a request … "convo-starters-sidebar"`, which is in all 20 failing runs.
- Got: `18x leaked window until shutdown [url = chrome://browser/content/browser.xhtml]` and `2x … MockEngineManager …`. The leak is the window the failed subtest never closed. The harness stamps a leak failure with the leaked window's *creation* time, so ordering by timestamp puts it before the exception that caused it.
- Workaround: `fx-tests task <id> --messages` on all 20 jobs.

## The messages of every failing run of one test

- Question: does every failing run of this test contain message X? (Which failure modes co-occur, per run.)
- Command: a loop of 20 `fx-tests task <id> --messages` calls (`all-tasks.txt` here).
- What could have shown it: `fx-tests test <path> --task-ids --messages`, one line per job with its message set, or a per-message count of the runs that contain it, not only of the runs where it comes first.
