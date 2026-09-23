## `zoom push` silently misreads the time format that `thread markers --list` prints

- Command: `profiler-cli zoom push 7m30s,8m4s --session <s>` (after `thread markers --list` printed times like `t=7m23s`)
- Expected: a zoom to 450 s..484 s, or an error saying only seconds are accepted.
- Got: `Zoom depth: 1` and a view of `ts-2→ts-3 (1s)`; the following marker query returned "No markers match", which read as "nothing happened there".
- Workaround: `zoom push 450,484`.

## Question: "in what order did these markers happen within the same second?"

- Command: `profiler-cli thread markers --search "smartwindow,browser.search.region,..." --list --limit 0`
- The list prints `t=8m3s` for every row, so a pref write, the pref read that follows it and a Glean `String::set` 1.5 ms later can't be ordered or spaced. Whether a read came immediately after a write (observer ran) or 49 ms later (lazy re-read) was the whole question.
- Workaround: `--json` and a python one-liner printing `start/1000` to 4 decimals.
- What would have answered it: millisecond (or sub-ms) times in `--list` output, at least when the rows span less than a few seconds, or a `--precise-times` flag.

## (review) `--search` with text copied from a `--list` row finds nothing

- Command: `profiler-cli thread markers --search "smartwindow.agent.toolbar.enabled: true" --list --session <s>`
- Expected: the `Preference Write  browser.smartwindow.agent.toolbar.enabled: true (Bool)` row that `--list` had just printed.
- Got: no match. The search tests each payload field (`prefName`, `prefValue`) separately, so the `name: value` label shown in the list can never match.
- Workaround: `--search "prefName:browser.smartwindow.agent.toolbar.enabled"`, then pick the row by eye.
