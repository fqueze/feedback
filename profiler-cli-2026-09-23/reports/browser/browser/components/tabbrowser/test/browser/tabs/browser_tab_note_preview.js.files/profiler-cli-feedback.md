## Marker times rounded to the second in text output

- Question: in what order, to the millisecond, did these markers (across threads) happen? The race here is ~500 ms wide, inside one second of an 8-minute profile.
- Command: `profiler-cli profile markers --session browser-browser_tab_note_preview.js-1 --search "CanonicalURL,-name:Preference Read" --limit 0` (same with `thread markers --list` and `marker info`)
- Expected: times with ms precision (e.g. `t=438.481s`, or `7m18.481s`) when the listed markers fall within the same second.
- Got: every row `t=7m18s` or `t=7m19s`; `marker info` says `Time: 7m18s - 7m18s (3.190μs)`. The chronological order is kept but the gaps are invisible.
- Workaround: `--json` and `jq '.markers[] | .start'`, merged with the test log's `thread markers --json` by a shell script.

## (review) Which `marker=N` link index is this handle?

- Question: does the report's link `marker=1372329` point at the marker it quotes? The reviewer has to map a session handle (`m-49`) to the link's marker index.
- Command: `profiler-cli marker info --session browser-review-browser_tab_note_preview.js-1 m-23 m-25 m-31 m-49 m-24`
- Expected: the text output shows the marker index (and the ms-precise start) next to each handle.
- Got: neither; the index is only in `--json` (`markerIndex`), and the time is `7m18s`. Needed a python script over `--json` for every profile. (Also hit the rounded-time issue above again.)
- Also: `profiler-cli --session X thread markers ...` fails with `unknown option '--session' (Did you mean --version?)`; `--session` is only accepted after the subcommand. The hint is misleading.
