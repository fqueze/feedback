# profiler-cli feedback

## Question: when was each window of this test destroyed, relative to the leak check?

- Command: `profiler-cli thread markers --session S --search "name:DOMWindow" --list --limit 0`
  on a resource-usage profile (48 min long).
- Expected: start and end of each DOMWindow interval marker, to the millisecond, so windows freed
  before and after `Completed ShutdownLeaks collections` can be told apart.
- Got: start rounded to the second (`t=31m20s`) and a duration; the end is not shown.
- Workaround: `--list --json` and a python one-liner computing `start + duration`. Note that
  `marker info --json` gives `start`/`end` while `--list --json` flatMarkers give `start`/`duration`.
- Could have shown: ms start and end in the list once the profile is longer than a minute.
