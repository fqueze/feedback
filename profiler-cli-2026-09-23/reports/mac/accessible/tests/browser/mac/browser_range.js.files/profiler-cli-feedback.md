## "In what order did these markers fire, within one millisecond?"

- Command: `profiler-cli thread markers --list --limit 0 --search "A11y Event,accessible-mac-event,TEST-" --session <s>` over a 3 ms zoom.
- Question: which `accessible-mac-event` notification came right after which `A11y Event - text removed`, and whether the waiter-resolving notification preceded the `TEST-UNEXPECTED-FAIL`. Some forty markers shared `t=19.455s`.
- Got: times printed at millisecond resolution, so every row read `t=19.455s` and the order could not be checked from the text output. I had to use `--json` and a script sorting `flatMarkers` by `start` and printing it to 0.1 µs.
- Could have shown: sub-millisecond times (for example `t=19455.201ms`) whenever the zoomed range is a few ms wide, or whenever consecutive rows share the same printed time.

## Default session directory not writable in a sandbox

- Command: `profiler-cli load <url> --session browser_range.js-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The message named `PROFILER_CLI_SESSION_DIR` and gave a working example, so it cost only one retry.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`.


## "Does this link's `marker=N` point at the marker I am looking at?" (review-browser_range.js)

- Command: `profiler-cli marker info m-159 --session <s>`, once per linked marker (about 30 in this report).
- Question: which marker index a handle has, to check a profiler link's `marker=N` against the marker it claims to show.
- Got: the text output has no marker index, so every check needed `marker info <m> --json` piped to a Python one-liner reading `markerIndex`.
- Could have shown: the marker index in the text output of `marker info` and in `thread markers --list` rows, or `profiler-cli load <profiler link>` printing the handle of the marker the link's `marker=N` selects.

## Loading a profiler link makes `profile-link.py` refuse the session (review-browser_range.js)

- Command: `profiler-cli load 'https://profiler.firefox.com/from-url/…'`, then `profile-link.py --session <s> --marker m-4`.
- Expected: a link, since the session knows the raw artifact URL it fetched.
- Got: `profile-link.py` exits because `profilePath` is the profiler.firefox.com URL. I had to reload the whole profile from the raw Taskcluster URL to build one link.
- Could have shown: the session storing the resolved artifact URL, or `profile-link.py` extracting it from a `from-url/` link.
