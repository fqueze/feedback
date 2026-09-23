## Question: when, to the millisecond, did each step of one subtest happen, next to the other threads' markers?

- Command: `profiler-cli thread markers --category Test --search browser_smartwindow_jump_to_bottom --list --limit 0 --session S` (and `marker info m-22 m-27 ...`)
- Expected: start times precise enough to order events inside one subtest (ms), since a whole subtest lasts about 1 s.
- Got: every row at `t=7m44s` / `t=7m45s` once the profile is longer than a minute; `marker info` also prints `Time: 7m45s`. The click, the scroll, the resize and the failed assertion all read `7m45s`.
- Workaround: `--json` and a Python one-liner printing `start/1000` with 4 decimals. Every later query in this investigation needed it.
- What would have answered it: seconds with 3 decimals in `--list` (e.g. `t=465.413s`), or a `--precise-times` flag.

## Question: which of these SetNeedStyleFlush markers came from my component's code?

- Command: `profiler-cli thread markers --search SetNeedStyleFlush --has-stack --list --limit 0 --session S`, then `marker stack m-N` for each of ~100 markers.
- Expected: a way to filter markers by a frame in their stack (e.g. `--stack-search ai-chat-content`), or a one-line stack summary column in `--list`.
- Got: `--search` only matches name/payload, so I scripted `marker stack` over every handle and grepped.
- Workaround: loop over `--json` handles calling `marker stack` per marker.

## (review) Loading a large CI per-test profile timed out

- Command: `profiler-cli load "<profiler.firefox.com from-url link to NwZkdKl_Q1yVweTLFcAGfA profile_browser_smartwindow_jump_to_bottom.js.json>" --session S`
- Expected: the profile loads, as three others from the same test did.
- Got: `Error: Profile load timeout after 60000ms (set PROFILER_CLI_LOAD_TIMEOUT_MS to override)`, and a half-started session to stop.
- Workaround: `PROFILER_CLI_LOAD_TIMEOUT_MS=240000`, reload. A longer default, or a timeout that only counts time without download progress, would have avoided the retry.

## (review) `marker info m-A..m-B` over the rows of a filtered `--list`

- Command: `profiler-cli marker info m-2..m-11 --json --session S`, taking the first and last handle of a `thread markers --search ... --list` output.
- Expected: the markers of those list rows (the guide says "Inclusive range of handles, e.g. consecutive list rows").
- Got: the handles in the list are not in list order (the first row was m-2, the last m-11, with m-49 in between), so the range returned other markers, some not markers of that list at all.
- Workaround: pass every handle from the `--json` list explicitly.
