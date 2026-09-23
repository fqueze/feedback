## Times above one minute lose sub-second precision in text output

- Command: `profiler-cli thread markers --search '...' --list --limit 0 --session <s>` and `profiler-cli marker info m-18 --session <s>` on a profile where the markers are at ~61 s.
- Expected: a timestamp with millisecond precision (e.g. `t=61.190s`), as for markers below 60 s (`t=47.466s`).
- Got: `t=1m1s` for every marker between 61.0 and 61.9 s, so a sequence of seven events 10-100 ms apart (setNetworkThrottling, safebrowsing-update-begin, two console errors, the fetch, a timer) all read `1m1s` and their order cannot be read from the list.
- Workaround: `marker info <m> --json` per marker and read `start`. The question was "in which order did these markers happen", which the list could have answered with `61.190s`-style times.

## Checking which marker a link's `marker=N` points to needs `--json` and a script (review-browser_browser_toolbox_network_throttling.js)

- Question: "is the marker the report quotes the one its link's `marker=N` opens?" — 17 links across 3 profiles.
- Command: `profiler-cli marker info m-15 --session <s>` after `profiler-cli load '<profiler link with marker=318071>'`.
- Expected: the text output to show the marker's index (e.g. `Index: 318071`), or `load` of a link carrying `marker=N` to print which marker that is and give its handle.
- Got: no index in the text output; `markerIndex` is only in `--json`. Workaround: `marker info <m> --json | python3 -c '...print(d["markerIndex"])'` for every marker.
- Also: `profile-link.py` refuses a session loaded from a profiler link ("load the Taskcluster URL itself"), while the review brief says to load the report's link; the two instructions conflict whenever a reviewer needs a new link.
