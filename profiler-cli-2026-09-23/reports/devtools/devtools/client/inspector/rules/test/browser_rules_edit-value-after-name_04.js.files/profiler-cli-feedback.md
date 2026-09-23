## Question: when, to the millisecond, was each step of the test log?

- Command: `profiler-cli thread markers --category Test --search browser_rules_edit-value-after-name_04.js --list --limit 0 --session ...`
- Expected: timestamps precise enough to order steps and zoom between them (e.g. `647318.8ms` or `10m47.318s`).
- Got: `t=10m47s` for 15 consecutive markers spanning ~200 ms; `marker info` also prints only `Time: 10m47s`. In a 12-minute profile the text output loses sub-second order, which is exactly the resolution needed to zoom between "TabOpen received" and "waiting for the tab".
- Workaround: `--json` and read `flatMarkers[].start`.
- What the output could show: milliseconds (at least in `marker info`), or precision scaled to the spacing of the listed markers.

## Question: which network loads failed, and with what status?

- Commands: `profiler-cli thread network --sort start --limit 0 --session ...` (zoomed on the failure), then `thread markers --search requestStatus:NS_ERROR_ABORT` and `thread markers --search NS_ERROR_ABORT` over the whole parent main thread.
- Expected: the one aborted document load flagged, e.g. `status=NS_ERROR_ABORT` in the `thread network` row, and the searches matching it.
- Got: `thread network` printed `???  cache=Unresolved  duration=217.51ms` for the request: the `requestStatus: NS_ERROR_ABORT` that explained the whole failure only shows in `marker info`. Both searches returned "No markers match": Network markers are shown as "Fields (raw payload, no schema)", and `--search` does not look at those fields.
- Workaround: `thread markers --category Network --search "Load " --list --limit 0 --json`, then a script tallying `data.requestStatus`.
- What the output could show: `requestStatus` (when it is not NS_OK) and `httpVersion` in each `thread network` row; `--search` matching raw payload fields of schema-less markers.

## `thread markers --search "Preference Read" --list --limit 0` timed out after 2 min

- Command: `profiler-cli thread markers --search "Preference Read" --list --limit 0 --session ...` on a parent main thread with 1.8 M markers, output piped to grep.
- Expected: the prefs read, or a quick refusal.
- Got: no output before the 2-minute shell timeout.
- Workaround: read the pref default from StaticPrefList.yaml instead.

