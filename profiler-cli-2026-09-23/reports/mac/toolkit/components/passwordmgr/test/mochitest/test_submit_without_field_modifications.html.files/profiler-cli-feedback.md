# profiler-cli feedback (test_submit_without_field_modifications.html)

## Log markers (the test's `info()` lines) print as "(empty)"

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` (thread: the mochi.test content GeckoMain), and `profiler-cli marker info m-4515`
- Expected: `INFO  Message was sent as expected after document user interaction`
- Got: `INFO  [(empty)] INFO: (empty)` in the list, and `Level: (empty)` / `Message: (empty)` in `marker info`.
  `marker info --json` has the data: `{"key":"message","value":"Message was sent as expected after document user interaction","formattedValue":"(empty)"}`.
  The formatter drops the value of every `Log` marker's fields. Half the test's log (all `info()` lines) is unreadable
  without `--json`.
- Workaround: `thread markers --category Test --list --json` piped to jq for `.fields[].value`.

## Question: the test's log, with its info() lines, in order

- Command: `profiler-cli thread markers --category Test --list --limit 0 --json | jq ...`
- What its output could have shown: the same list, with the Log markers' messages instead of "(empty)".

## `thread markers --list` is not chronological, and its times are rounded to the second

- Command: `profiler-cli zoom push 260.60,260.80` then `profiler-cli thread markers --search passwordmgr --list --limit 0` (parent GeckoMain, profile of task YpbqjFkaRaecRau8Yrd1cw)
- Expected: a flat chronological list (as the guide says), with times precise enough to order markers 1 ms apart.
- Got: rows in the order m-4526, m-4528, m-4532, m-4530, m-4533, m-4529, m-4525, m-4534, m-4531, m-4527, every one printed `t=4m21s`.
  Sorted by `.start` from `--json`, the real order is m-4526, m-4528, m-4532, m-4530, m-4533, m-4529, m-4525, m-4534, m-4531, m-4527
  only by coincidence for the first half; m-4525 (260.7094) is listed after m-4529 (260.6993) but before... in short, the text
  gives no way to tell, and the order of these notifications 1 to 10 ms apart was the whole diagnosis.
- Workaround: `--json | jq '.flatMarkers | sort_by(.start)[] | ...'`, printing `.start/1000`.

## Question: the order of events a few ms apart

- Command: `thread markers --search ... --list --json | jq -r '.flatMarkers | sort_by(.start)[] | "\(.handle)\t\(.start/1000)\t\(.name)\t\(.label)"'`
- What its output could have shown: millisecond (or better) times in the text list, e.g. `t=260.7094s`, whenever the view is short.

## Resource-usage profile: `profile info` says "No significant activity", `counter list` says "No counters"

- Command: `profiler-cli load .../YpbqjFkaRaecRau8Yrd1cw/.../profile_resource-usage.json`, `profile info`, `counter list`
- Expected: the machine's CPU use over time, which the brief says this profile holds.
- Got: `CPU activity over time: No significant activity.` and `No counters in this profile.` The data is in 19,010 `CPU Use`
  markers (`cpuPercent`, `idle_pct`, ...), which only `thread markers --search "name:CPU Use" --list --json` shows.
- Workaround: that command, zoomed on the failure, with jq on `.data`.

## Correction to "`thread markers --list` is not chronological" above

- That entry is wrong on ordering: the text list was chronological; I misread it because marker handles are not
  monotonic in time (m-4526 at 260.6307 comes before m-4525 at 260.7094). Only the second half stands: every row printed
  `t=4m21s`, so the text alone could not show the order of events 1 to 10 ms apart, nor that the handles were out of
  sequence by design. Millisecond times in the text (e.g. `t=260.7094s`) would have answered it without `--json`.
