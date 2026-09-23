## `thread markers --list` shows times rounded to the second

Command: `profiler-cli thread markers --category Test --search browser_tab_splitview_contextmenu --list --limit 0 --session <s>`
Expected: marker start times precise enough to order and space events (ms), e.g. `t=328.944s`.
Got: `t=5m29s` for every marker in the 5-second window that matters; "checking for open popups" and the failure 5 s later are indistinguishable from the test's own steps.
Workaround: `--json` and print `start/1000` with a script. Question the default output could have answered: "how far apart are these log lines".

