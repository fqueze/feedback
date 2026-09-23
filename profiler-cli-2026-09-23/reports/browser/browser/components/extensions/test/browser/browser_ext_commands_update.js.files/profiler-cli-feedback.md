## Question: at what exact time did this marker happen?

- Command: `profiler-cli marker info m-6 m-88 m-16 --session <s>` (per-test profile, 1m14s long)
- Expected: a timestamp precise enough to compute the gap between the test's last check and the harness's vsync failure (ms resolution, like `t=65.319s`).
- Got: `Time: 1m5s (instant)` for all of them; the `--list` rows also say `t=1m5s` for ~80 markers spread over 300 ms. Past one minute the text output rounds to whole seconds.
- Workaround: `marker info ... --json` and read `.start` (ms).

## Question: which document is this marker about? (markers sharing one innerWindowID)

- Command: `profiler-cli thread markers --session <s> --search 'innerWindowID:4294967338' --list` and `--search 4294967338`, on the WebExtensions GeckoMain thread, which has 3,765 `RefreshDriverTick waiting for paint` markers and a `DocumentLoad` marker carrying that innerWindowID.
- Expected: those markers, or at least the `DocumentLoad` naming the document's URL.
- Got: `No markers match the specified filters.` The field shows up only as a raw field (`Fields (raw payload, no schema): innerWindowID`), and search does not look at raw fields.
- Workaround: `--list --limit 0 --json`, group by `.data.innerWindowID` in a script, then `marker info --json` on the `DocumentLoad` candidates and compare their `rawFields`. A `--search` covering raw fields, or the page URL resolved from the profile's pages table next to innerWindowID, would have answered it.

## (review) Note on "which document is this marker about?"

- Command: `profiler-cli thread markers --session <s> --search "waiting for paint" --group-by "field:innerWindowID"`
- Got: the counts per innerWindowID (`4294967338: 3765 markers`), which answers the question above without a script. `--search` still ignores raw fields, and the URL is not resolved, so matching the ID to `DocumentLoad` still takes `marker info --json` and reading `rawFields`.

## (review) `--session` before the subcommand is rejected

- Command: `profiler-cli --session <s> thread list`
- Expected: the same as `profiler-cli thread list --session <s>`.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand.
