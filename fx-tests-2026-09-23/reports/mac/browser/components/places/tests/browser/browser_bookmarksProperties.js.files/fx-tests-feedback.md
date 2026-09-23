## `task --messages` mixes expected results in with the failures

- Command: `fx-tests task SxD0uPUsTem46whPB24ZRQ --profiles --messages`
- Expected: the failing messages of `browser_bookmarksProperties.js`, with expected (TEST-KNOWN-FAIL / todo) results left out or labelled.
- Got: `Assertion count 2 is greater than expected range 0-0 assertions.` and `changed preference: sidebar.history.sortOption` listed next to the leak with the same `2x` weight. Both are TEST-KNOWN-FAIL by design in `browser-test.js` (assertion counts are `todo: true`; pref changes are `todo` when `comparePrefs` is off), so they cannot fail a job. I only found that out by reading the TEST-* markers in the profile and the harness code.
- Workaround: check each message's TEST-* marker status in the per-test profile.

## Question: which other test fails in exactly the same jobs as this one?

- Question: is this failure always paired with another test's failure in the same job? Here, `browser_bookmarksProperties.js`'s leak shows up in exactly the jobs where the next test, `browser_bookmarks_change_title.js`, times out: 225 of 225 jobs, with the same multiplicity.
- Command: `fx-tests test <path> --task-ids --limit 0 --issue 1` for both tests, then a Python script to intersect the two task-ID sets.
- What the output could have shown: a "co-failing tests" line in `fx-tests test` ("in 225 of 225 failing jobs, `browser_bookmarks_change_title.js` also failed; it runs right after this test"). That points straight at a neighbour's timeout or leak.

## Question: where is the retention path of a shutdown leak?

- Question: what keeps the window alive? `ShutdownLeakPathFinder` logs the retention path as the stack of the leak failure.
- Command: `fx-tests task SxD0uPUsTem46whPB24ZRQ --profiles`. For `browser_bookmarksProperties.js` it lists no profile, because the leak check runs after the next test's per-test profile has been captured.
- What the output could have shown: a pointer to the resource-usage profile's `TestStatus` FAIL marker, whose stack is the retention path (`profiler-cli marker stack`). I only found that marker by searching the resource-usage profile.
