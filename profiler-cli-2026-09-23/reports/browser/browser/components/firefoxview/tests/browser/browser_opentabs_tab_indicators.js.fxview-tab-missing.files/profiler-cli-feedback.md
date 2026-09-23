## Question: did the popup open before or after the pointer left the button, 27 ms later?

- Command: `profiler-cli thread markers --search "popup,mousedown,..." --list --limit 0 --session S` after `zoom push 202.5,211.1`.
- Expected: ms-resolution start times in the list.
- Got: `t=3m23s` on every row, so rows 5 to 70 ms apart could not be ordered from the text output.
- Workaround: `--json`, then `.flatMarkers[].start/1000` in a small script (same as the other report of this test logged).

## Question: the test's own log, one line per message

- Command: `profiler-cli thread markers --category Test --search browser_opentabs_tab_indicators.js --list --limit 0`
- Got: 25,379 lines for 606 markers, from multi-line `Console message` stacks (FxA NO_ACCOUNT).
- Workaround: `--json` and print the first line of each label.
