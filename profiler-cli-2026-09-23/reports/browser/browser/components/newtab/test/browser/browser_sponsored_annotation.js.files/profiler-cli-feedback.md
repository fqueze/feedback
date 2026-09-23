## TEST-UNEXPECTED-FAIL missing from the test-file search

- Command: `profiler-cli thread markers --category Test --search browser_sponsored_annotation --list --limit 0 --session <s>` (parent main thread, per-test profile of a mochitest).
- Expected: the test's log, including its `TEST-UNEXPECTED-FAIL`, as the brief says.
- Got: every TEST-PASS and INFO, but no `TEST-UNEXPECTED-FAIL` (m-16, "Uncaught exception in test - Waiting for the expected link to be rendered - timed out after 50 tries."). Its text does not contain the file name, so a search on the file name misses it. I first read the run as having no failure.
- Workaround: `thread markers --category Test --list --limit 0 | grep -iE "fail|timed out"`, then a `zoom push` around it.

## Which browser a JSActor message went to

- Question: did `ActivityStream:MainToContent` at t=20.293 go to the test's about:home or to the preloaded about:newtab? Both are in the same Privileged Content process.
- Command: `profiler-cli marker info m-1620 --json` (and the matching `ReceiveMessage` on the content thread).
- Got: only `actor` and `name` fields. There is no innerWindowID or browsing context, so the target could only be inferred by counting sends (pairs for broadcasts, one for a single target).
- What could have answered it: the marker's innerWindowID/URL if Gecko records one, shown in `marker info`.

## `name:INFO` matches Runnable names

- Command: `thread markers --search "...,name:INFO,..." --list`.
- Got: hundreds of `Runnable PWindowGlobal::Msg_SetClientInfo` rows, because `name:` is a substring match on the payload's `name` too. I had to pipe through grep -v.
- Expected: some way to match the marker name exactly (for example `name:=INFO`).
