## Question: which document are these per-window markers for?

- Command: `profiler-cli thread markers --session <s> --search "innerWindowID:4294967338"` (on the WebExtensions GeckoMain thread)
- Expected: the markers carrying that innerWindowID (RefreshDriverTick waiting for paint, FirstNonBlankPaint, nsRefreshDriver, DocumentLoad...), to tie a leaked refresh driver to its popup.html URL.
- Got: "No markers match the specified filters", although `--group-by field:innerWindowID` on the same markers groups them by that key (476 under 4294967338). `innerWindowID` is a raw payload key without schema, so `field:value` search seems not to reach it.
- Workaround: `marker info` on candidate DocumentLoad/FirstNonBlankPaint markers one by one to read their innerWindowID, and `--list --json` + a script to count per innerWindowID.
- What would have answered it: `--search innerWindowID:<id>` working, or the marker list resolving innerWindowID to the page URL (the profile's pages table has it).

## Question: which process were these IPC markers sent to? (review)

- Command: `profiler-cli thread markers --session <s> --search PVsync --group-by otherPid` (parent GeckoMain, zoomed on the vsync wait)
- Expected: groups per recipient pid, or an error saying `otherPid` is not a known grouping key.
- Got: a single `(no value): 327 markers` group, with no warning. `--group-by field:messageType,field:recvThreadName` works, so the `field:` prefix was missing, but an unknown key silently turns into "(no value)". The default aggregate also shows only `IPCOut` and does not break it down by message type, which is what mattered here (PVsync vs PVsyncBridge).
- Workaround: `--list --limit 0 --json` plus a script counting `messageType`/`recvThreadName`.
- What would have answered it: reject or warn on a `--group-by` key that no marker has, or suggest `field:<key>`; for IPC markers, show message type and recipient in the default aggregate.
