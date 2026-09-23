## (review) Question: "in what order, to the millisecond, did these markers happen?"

- Command: `profiler-cli thread markers --session <s> --search "findLogins,AutoComplete:,PopupAutoComplete" --list --limit 0`
- Expected: each row's start time precise enough to order IPC messages a few ms apart (e.g. `t=1688.597s`).
- Got: `t=28m8s` / `t=28m9s`, one-second resolution; `--list --json` returns the aggregated `byType` summary, not the rows. No option changes the time format.
- Workaround: pipe the handles from the text list into `marker info <handles> --json` and print `start` with a script, for every thread and every profile.

## (review) Session header names a different thread than `thread info`

- Command: `profiler-cli load '<profiler.firefox.com link with thread=xp>' --session <s>` on task Olt6OoriSiyMCMEoqebQYw
- Expected: the header to name the selected thread as `thread info` and `profile markers` do: `t-57 https://example.com (18/18)`.
- Got: `Selected thread: t-57 (GeckoMain, https://example.org (3/14))` in the load status and every command header, while `thread info` says `Name: https://example.com (18/18)`.
- Workaround: confirmed with `profile markers --search` that t-57 holds the markers the link points at.
