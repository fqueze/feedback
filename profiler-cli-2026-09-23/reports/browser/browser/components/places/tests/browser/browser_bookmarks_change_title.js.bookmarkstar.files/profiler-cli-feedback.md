## Which markers belong to one window (innerWindowID), in ms order, over a 100 ms range

- Command: `profiler-cli thread markers --session <s> --search "innerWindowID:364" --list` (zoomed on 67.72,67.815)
- Expected: the refresh ticks, reflows and DOM events of window 364, with ms timestamps and their innerWindowID, since `marker info --json` shows `rawFields: [{key: "innerWindowID", value: "364"}]`.
- Got: `0 markers (filtered from 1294)`. Without it, the default `--list` prints every row as `t=1m8s` with no innerWindowID column, so ticks of different windows 10 ms apart cannot be ordered or told apart.
- Workaround: `--list --limit 0 --json` then a Python filter on `data.innerWindowID` and `start`, for every range I read (4 profiles).
- Question the default output could have answered: "between the navigation commit and the error, did this window get a reflow, or only skipped `RefreshDriverTick waiting for paint`?"

## Was the machine saturated at time t (resource-usage profile)

- Command: `profiler-cli profile info --session <s>` and `profiler-cli counter list --session <s>` on `profile_resource-usage.json`
- Expected: the machine CPU over time, which the brief says this profile holds.
- Got: `CPU activity over time: No significant activity.` and `No counters in this profile.` The data is in 13,506 `CPU Use` markers (`cpuPercent`, `idle_pct`) that neither command mentions.
- Workaround: `thread markers --search "name:CPU Use" --list --json` over a zoomed range.
- Question the default output could have answered: "what was the machine's CPU use while this test ran".

## (review) Which document a Reflow without innerWindowID belongs to

- Command: `profiler-cli thread markers --session <s> --list --limit 0 --json`, filtered on `name` Reflow / DoFlushPendingNotifications, in L13PAHgjQgKN6bWHfp8-3w (3.62–3.75 s) and I6-ZPTxlT5KgML-UT8EWDQ.
- Expected: each Reflow row to say which document reflowed, since the question is "did window 28 reflow before the error".
- Got: some `Reflow (sync)` rows carry no innerWindowID at all (e.g. 3729.27, 3105.55), with no URL either; only `marker stack` on each one (nsInputStreamPump::OnStateTransfer, `MozSVGAsImageDocumentLoad`) shows they are SVG-as-image documents.
- Workaround: one `marker stack` call per window-less reflow.
- Question the default output could have answered: "which document did this reflow lay out" — a document URL or "image document" in the row would have.
