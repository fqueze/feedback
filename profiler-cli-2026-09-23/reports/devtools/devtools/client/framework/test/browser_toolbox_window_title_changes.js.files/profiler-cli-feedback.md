## Question: in which order, to the millisecond, did the toolbox window unload and the inspector TypeError happen?

- Command: `profiler-cli thread markers --search DOMEvent --list --limit 0 --session browser_toolbox_window_title_changes.js-1` (profile is 7m16s long)
- Expected: marker start times precise enough to order events a few ms apart (e.g. `t=434.661s`).
- Got: every row shows `t=7m14s` / `t=7m15s` — times rounded to the second once the profile is over a minute long, so the list could not say whether `DOMWindowClose` (434.6615s) came before the console TypeError (434.6813s).
- Workaround: `--json` and a python script over `flatMarkers[].start`. Same for `marker info`, which also prints `Time: 7m15s`.
- What would have answered it: millisecond resolution in the list's time column (e.g. `7m14.661s`), at least when `--list` is used.

## `marker info --json` records have no `handle` field

- Command: `profiler-cli marker info m-76 m-79 m-80 --json --session ...`
- Expected: each record to carry its `m-N` handle so a multi-marker call can be matched back.
- Got: `handle` absent (had to rely on order).

## Question (review): at what time is the one sample in `inspector.js!onRootNodeAvailable` between 433.0s and 434.7s?

- Command: `profiler-cli thread samples-top-down --search onRootNodeAvailable --session review-browser_toolbox_window_title_changes.js-1` after `zoom push 433.0,434.7`
- Expected: some way to get the time of the matching sample(s), to tell whether the inspector was working before or after the toolbox destroy began (433.674s).
- Got: a call tree with percentages only; no sample times. Took 10 `zoom clear`/`zoom push`/`samples-top-down` rounds to bisect it to 433.660-433.665s.
- What would have answered it: a `--list` (or `--times`) option on the samples commands printing each matching sample's time, like `thread markers --list` does.
- Note on the earlier entry: multi-marker `marker info --json` records do carry the handle, as `markerHandle` (not `handle`), inside a top-level `markers` array.
