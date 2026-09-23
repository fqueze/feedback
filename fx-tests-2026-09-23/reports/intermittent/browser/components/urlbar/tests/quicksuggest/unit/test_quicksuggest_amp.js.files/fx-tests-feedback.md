## Question: what timeout did this test run under on each config, and how close to it are its passing runs?

- Command: `fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_amp.js --durations --limit 0`
- Expected: per-config durations next to the effective timeout (base 30 s x taskcluster `timeoutfactor` x manifest `requesttimeoutfactor`), and ideally split by parallel vs sequential/retry phase.
- Got: durations only. I had to download three `live_backing.log`s and grep `Using harness timeout of` to learn that linux2404-64-artifact/debug and macosx1500-aarch64-vms/debug run at 30 s while linux2404-64/debug runs at 60 s. The durations also mix phases: on linux2404-64-artifact/debug every parallel run times out, so its "median 15959 ms" is really the sequential retry's duration and hides that the parallel run needs > 30 s.
- Oddity: linux2404-64/debug-xpcshell shows a passing median of 64051 ms, but that config's timeout is 60 s (the parallel run of task ApGo6zCGSHqksMvQKIDLgw timed out at 62180 ms). I don't know which runs these are.
- Workaround: grep the task logs.
