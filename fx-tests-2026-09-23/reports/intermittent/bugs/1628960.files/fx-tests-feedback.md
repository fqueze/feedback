# fx-tests feedback (bug 1628960)

## `fx-tests task` does not list the failure that turned the job orange when it is a leakcheck
- Command: `fx-tests task LVHpD25IRFC29HfNoWbSRg --profiles`
- Expected: the `TEST-UNEXPECTED-FAIL | leakcheck | default 2800 bytes leaked (...)` line. It is what made the job orange (TinderboxPrint `2271/1/3`).
- Got: "1 failing", naming only `test_group_overscroll_handoff.html`, a failure that passed on retry and that every Mac job has. Nothing about leakcheck.
- Workaround: I downloaded `live_backing.log` and grepped it. Question to log: "which harness-level failures (leakcheck, shutdown, crash) made this job fail?"

## `intermittent --history` hides where its data begins
- Command: `fx-tests intermittent --bug 1628960 --since 150 --history`
- Got: zeros from 2026-04-26 to 05-24, then counts from 05-25. That reads like a step change on 05-25, but 05-25 is where Treeherder's retention of about 120 days ends.
- Expected: something like "data before 2026-05-25 not retained".
- Workaround: none. I had to reason it out.

## No way to get the passing runs of a test to compare with the failing ones
- Question: "the green jobs that ran test_group_zoom-2 on mac debug, so I can compare their logs with the failing jobs' logs."
- `fx-tests test --task-ids` lists failing tasks only. I used the Treeherder REST API (push → jobs), downloaded 28 logs, and found which ones ran the test by grepping.
- It could show one or more passing task IDs per config next to the failing ones.

## The resource-usage profile lacks the leakcheck failure lines
- Command: `profiler-cli thread markers --search "missing output line,bytes leaked" --list` on LVHp's `profile_resource-usage.json`
- Got: 0 markers, although `leakcheck | Processing log file ...` INFO lines are markers. So the failure line cannot be linked from the profile.
