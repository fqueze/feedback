
## Question: in what order did markers less than a second apart happen (a JS error against the test's own steps)

- Command: `profiler-cli zoom push 737.5,738.5 --session <s>` then `profiler-cli thread markers --session <s> --list --limit 0` (resource-usage profile of task cvG0LS-xRJurhlDv3n8Juw)
- Expected: when the view is zoomed to 1 s, show start times at ms precision (e.g. `t=738.181s`), so a TypeError can be set against the INFO/PASS markers around it.
- Got: every one of the 169 markers shows `t=12m18s`. The list order is the only clue, and interval markers are mixed in.
- Workaround: `profiler-cli marker info m-… m-… --json` and read `start` for each handle.
