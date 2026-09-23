## Question: in which order did markers a few ms apart happen, past the first minute of a profile?

- Command: `profiler-cli thread markers --session browser_multiple_ico.js-2 --list --limit 0 --search "name:BrowserTestUtils,name:ReceiveMessage,name:RefreshDriverTick,name:Image Load,TabAttrModified"` with `zoom push 60.82,60.90` (profile of task dEFj4sspR2ab7oQIpLsSCw).
- Expected: start times with millisecond precision, as below one minute (`t=53.463s`).
- Got: every row printed `t=1m1s`, so the 49 ms window's ordering (switchTab resolved, SetIcon received, openNewForegroundTab ended) could not be read. Durations kept their precision; start times did not.
- Workaround: `--json` and a script printing `start`/`duration` — but the JSON rows had no description, so I needed both outputs side by side to know which `ReceiveMessage` was `Link:SetIcon`.
- What the default output could show: `t=60.868s` (or `1m0.868s`) whenever the view is shorter than a few seconds.

## Question (review): same as above, from the reviewer. In which order did markers a few ms apart happen, past the first minute?

- Command: `profiler-cli thread markers --session review-browser_multiple_ico.js-1 --list --limit 0 --search "name:BrowserTestUtils,name:RefreshDriverTick,Link:,…"`, with `zoom push 65.10,65.20` (EQCi3CrpTeem9cYJ3SaXPA).
- Expected: start times precise enough to put a 100 ms window in order.
- Got: every row showed `t=1m5s`, for the second time on this report.
- Workaround: `--json` with a script that prints `start` and `duration` next to `label` (`review/tl.py`).
