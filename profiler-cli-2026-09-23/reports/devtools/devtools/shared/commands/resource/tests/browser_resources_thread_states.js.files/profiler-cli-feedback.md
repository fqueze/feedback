## Marker times past one minute are printed to the second only

- Question: in what order, and how many ms apart, did a pref write, two pref reads and a console error happen within the same second (t≈91.15s)?
- Command: `profiler-cli thread markers --session <s> --category Test --search browser_resources_thread_states --list --limit 0`
- Expected: times with ms resolution (like `t=52.740s` below one minute).
- Got: every marker after 60s shows `t=1m31s`, so ordering within the second is unreadable.
- Workaround: `--json` and print `start` myself. Printing `t=1m31.153s` would have answered it.

## Loading a per-test profile selected a content-process thread

- Command: `profiler-cli load <profile_browser_resources_thread_states.js.json from task MUc9-ldYRuSrmi-cOAke4A> --session <s>`, then `thread markers --search ...`
- Expected: the parent-process GeckoMain selected by default (it holds the test log), as happened for the Linux profile of the same test.
- Got: `t-33 (GeckoMain, Privileged Content)` selected; the marker search returned 0 matches with no hint that another thread has them.
- Workaround: `profile info --search "Parent Process"`, then `thread select t-0`.

## The tests that ran before this one, with status and times

- Question: which tests ran earlier in this per-test profile's browser session, with PASS/FAIL and start/end times?
- Command: `profiler-cli thread markers --category Test --search "type:Test" --list --limit 0` matches TestStatus markers too (thousands of TEST-PASS), so I filtered `name == 'test'` from `--json`.
- What would have answered it: a way to list only `Test`-type markers (the per-test `test` intervals), or an exact marker-type filter.
