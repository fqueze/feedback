## Which of two markers in the same millisecond came first, or whether one is inside another

Question: did `WillChangeBrowserRemoteness` run before or after `PBrowser::Msg_NavigationFinished`; did the
`TimingDistribution::start urlbar.autocomplete_first_result_time` fall inside the `mousedown` or the `focus` DOMEvent.

Command: `profiler-cli thread markers --search "WillChangeBrowserRemoteness,PBrowser::Msg_NavigationFinished" --list --session <s>`
and `profiler-cli marker info m-604 m-190 m-637 --session <s>`

Expected: enough precision to order them (or an explicit "contains/contained in" relation).
Got: `t=49.590s` / `t=49.591s` and `Time: 49.631s - 49.631s` for all three; the order that decides the diagnosis is
sub-millisecond and invisible in the text output.

Workaround: `--list --json` / `marker info --json` piped through python to print `start`/`end` with 3 decimals of ms.
What the default output could show: times with microsecond precision in `--list` when a zoom is under ~1 s, or
`start`/`end` to 0.001 ms in `marker info`.

## Markers whose stack contains a given function

Question: which `SetNeedStyleFlush` markers in a range were caused by `setPageProxyState` / `setURI` /
`AsyncTabSwitcher.updateDisplay`.

Command: `profiler-cli thread markers --search SetNeedStyleFlush --list` then `marker info m-745..m-814` and grep.
Expected: a `--stack-contains <function>` filter on `thread markers`.
Got: no way to filter on stack frames; had to dump 70 full stacks (80 KB) and grep them.

## Which marker index each listed marker has (reviewing a report's `marker=N` links) — review-browser_glean_telemetry_bounce.js

Question: do the ~25 `marker=N` links of a report point at the markers it quotes.
Command: `profiler-cli thread markers --search "<names>" --list --json --session <s>` for the handles, then
`profiler-cli marker info <h1> <h2> … --json --session <s>` for `markerIndex`.
Expected: `markerIndex` in the `--list` output (text or JSON), or a `--marker-index N` lookup.
Got: `--list` has handles only, so each range needed a two-step script. `marker info --json` also changes shape
with the handle count: one handle gives the record itself, several give `{markers: [...]}`, which broke the
first script.
Workaround: a zsh+python helper per range.
What the default output could show: the index next to each handle in `--list`, and one JSON shape for
`marker info` whatever the number of handles.
