## Question: when, to the millisecond, did each line of the test's log happen?

- Command: `profiler-cli thread markers --category Test --search browser_ext_simple --list --limit 0 --session <s>` (and `marker info m-15`) on a 4m29s per-test profile.
- Expected: timestamps with sub-second precision (e.g. `t=260.546s`), so the ~22 ms test body and the 8.27 s wait for vsync can be read off the list.
- Got: every row shows `t=4m21s` or `t=4m29s`; `marker info` says `Time: 4m21s`. Twenty markers spanning 22 ms, and the 8.27 s gap, are indistinguishable.
- Workaround: `--json` and a Python one-liner printing `start/1000`.

## Question: which document are these 14,799 `RefreshDriverTick waiting for paint` markers for? (review)

- Command: `profiler-cli thread markers --search "waiting for paint" --session <s>` (and `--list`) on the WebExtensions GeckoMain thread.
- Expected: the aggregate, or the list, to say which document or innerWindowID each marker belongs to, e.g. a per-innerWindowID count, with the URL of that window's `DocumentLoad`.
- Got: names and times only; the innerWindowID shows only in `marker info` under "raw payload", one marker at a time.
- Workaround: `--list --limit 0 --json`, a Python `Counter` over `data.innerWindowID`, then matching that ID against the `DocumentLoad` markers' JSON by hand.
