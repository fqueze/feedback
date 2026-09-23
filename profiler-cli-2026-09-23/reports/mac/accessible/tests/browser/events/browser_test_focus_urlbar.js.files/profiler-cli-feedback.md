## `--search` does not match the text a DOMEvent row displays

- Command: `profiler-cli thread markers --search "blur - input,Collapsed" --list --limit 0 --session …` (zoomed on 18.28–18.285 s)
- Expected: the `DOMEvent` row that `--list` prints as `blur - input@1544a27a0 class="urlbar-input textbox-input" id="urlbar-input"`.
- Got: `No markers match the specified filters.` (the TEST-PASS "Collapsed …" row did not match either, through the same comma list).
- Workaround: `--search DOMEvent` and grep the listed text. The displayed label (event type + target) is what one copies from a previous listing, so it is the natural search term.

## Default session directory not writable under the sandbox

- Command: `profiler-cli load <taskcluster url> --session …`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a clear hint to set `PROFILER_CLI_SESSION_DIR`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`. The hint worked; a fallback to `$TMPDIR` when the default is not writable would have saved the round trip, and every later command needs the variable too.

## Question: which marker is the `marker=N` of a link? (review-browser_test_focus_urlbar.js)

- Needed for: checking a report's links, where each `marker=N` is a marker index.
- Command: `profiler-cli thread markers --list --limit 0 --json --session …` (zoomed on 18.25–18.30 s). Its `flatMarkers` entries carry `handle`, `name`, `start` and `fields`, but no `markerIndex`. No command looks up a marker by its index.
- Workaround: `marker info m-1..m-256 --json`, repeated in eight chunks to cover every handle in the zoom, then a script to map `markerIndex` to marker. Two such dumps were needed per profile.
- What would have answered it: `marker info --index 579644`, or a `markerIndex` field in the `--list --json` rows.
