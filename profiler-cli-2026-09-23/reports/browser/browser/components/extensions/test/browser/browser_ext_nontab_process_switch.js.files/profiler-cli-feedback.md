## `--search` does not match numeric payload values (innerWindowID)

- Command: `profiler-cli thread markers --session browser-nontab_process_switch-1 --search "innerWindowID:4294967338" --list` (also a bare `--search 4294967338`)
- Expected: the markers of that window (DocumentLoad, RefreshDriverTick, ViewManagerFlush, DOMEvent all carry `innerWindowID`), to answer "which document is this stuck refresh driver's?"
- Got: `0 markers`. `--group-by field:innerWindowID` does see the field, so the value exists.
- Workaround: listed DocumentLoad markers and ran `marker info` on candidates one by one until one's innerWindowID matched.

## (review) A link's `markerSearch` can match nothing, and nothing flags it

- Question: "does the report's link 1 (`markerSearch=PASS —,FAIL —`) show the per-test results it quotes?"
- Command: `profiler-cli thread markers --session browser-review-nontab-1 --thread t-0 --search 'PASS —,FAIL —' --list`
- Expected: the 24 `test` markers, whose list label reads `PASS — <path>` / `FAIL — <path>`.
- Got: `0 markers`. The label is composed from the `status` and `test` fields; search does not see it. Working equivalent: `--search 'status:FAIL,status:PASS,-name:TEST-PASS,-name:TEST-UNEXPECTED-FAIL,-name:TEST-KNOWN-FAIL'`.
- Could have shown: `profile-link.py --search` (or `load` of a link) warning when the search matches no marker in range; or search matching the printed label, since that is what users copy.

## (review) profile-link.py refuses a session loaded from a profiler.firefox.com link

- Command: `profile-link.py --session browser-review-nontab-1 --thread t-0 --range 0,185.1 --search ...`, the session having been loaded from the report's own `https://profiler.firefox.com/from-url/<tc url>/marker-table/?...` link, as review-brief.md says to do.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself".
- Expected: the from-url link embeds the Taskcluster URL; extract it. As is, fixing a link during review costs a second 0.6 GB load of the same profile.
