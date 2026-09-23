## Question: which stack, and which preceding test group, is behind each occurrence of a bug that names no test (bug 1986118)

- Command: `fx-tests intermittent --bug 1986118 --limit 0`
- Expected: per occurrence, the crash/ABRT signature's top frames (or the frame below the libc wait) and the last test (or test group) that ran in the browser whose child hung. That is what distinguishes the failure modes sheriffs lump under one generic summary such as `ABRT … __futex_abstimed_wait_common64`.
- Got: task ids, job names and platforms only. The "Failure messages" and "Tests named" blocks list unrelated test failures from the same jobs.
- Workaround: I downloaded 24 `live_backing.log`s and grepped around `hanging at shutdown; attempting crash report`. To attribute the hang to a browser, I matched the parent PID to its Marionette start and stop lines (wpt runs 2 browsers in parallel, so the log interleaves them). Script: `1986118.files/lastgroup.sh`. About 10 minutes and a lot of tokens.

## Question: how often does a wpt directory run on a config, to turn annotation counts into a failure rate

- Command: `fx-tests test /webaudio/the-audio-api/the-pannernode-interface/test-pannernode-automation.html`
- Expected: runs per config for a wpt test, as for mochitest and xpcshell.
- Got: `No test path in the xpcshell and mochitest 21-day data contains …`. wpt is not covered.
- Workaround: none. The report gives annotation counts without a rate.
