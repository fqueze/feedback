# profiler-cli feedback

Aggregated from 1771 `profiler-cli-feedback.md` files. Counts are the number of reports mentioning each item; each example is one real report. Resource-usage profile issues are in [resource-usage-profile-feedback.md](resource-usage-profile-feedback.md), and gaps in Firefox's profile data in [firefox-profile-data-feedback.md](firefox-profile-data-feedback.md). 35 reports about problems outside profiler-cli aren't counted. Sources for each item: [profiler-cli-feedback-sources.md](profiler-cli-feedback-sources.md).

## 1. Marker times rounded to whole seconds once past one minute (707)

`thread markers --list` and `marker info` drop milliseconds once past 60 s: two markers 25 ms apart (63.871 s test end, 63.896 s launch failure) both print `t=1m4s`, so their order can't be read without `--json`.

- **Question:** Did the test end before or after the launch failure, 25 ms later?
- **Command:** `profiler-cli thread markers --session <s> --search "early_shutdown.js,Failed to launch" --list`
- **Expected:** Timestamps precise enough to order two markers 25 ms apart.
- **Got:** Both shown as `t=1m4s` / `Time: 47.432s - 1m4s`; below one minute output keeps ms (`t=44.598s`).
- **Suggestion:** Keep milliseconds in the minutes form, e.g. `1m3.896s`.
- **Example from:** [devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_HeapAnalyses_deleteHeapSnapshot_03.js.files/profiler-cli-feedback.md](reports/devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_HeapAnalyses_deleteHeapSnapshot_03.js.files/profiler-cli-feedback.md)

## 2. No statistics of numeric marker payload fields over a view (343)

On a resource-usage profile (no counters), machine CPU exists only as ~450 `CPU Use` markers per 45 s with a `cpuPercent` field. `thread markers --search "name:CPU Use"` aggregate shows only count and duration stats, so CPU mean/min/max over the zoom needed --json plus a script.

- **Question:** Was the machine saturated while this test ran?
- **Command:** `profiler-cli thread markers --search "name:CPU Use"`
- **Expected:** Mean, min and max of cpuPercent over the zoomed range, or per N-second bucket, like `counter info` gives.
- **Got:** Only duration stats of the CPU Use markers, not field stats; `counter list` says "No counters in this profile".
- **Suggestion:** Show mean/min/max (or a sparkline) of numeric payload fields in the aggregate view, or expose resource-usage CPU Use as a counter.
- **Example from:** [intermittent/testing/web-platform/tests/html/browsers/the-window-object/window-aliases.html.files/profiler-cli-feedback.md](reports/intermittent/testing/web-platform/tests/html/browsers/the-window-object/window-aliases.html.files/profiler-cli-feedback.md)

## 3. Default session directory not writable in sandbox (EPERM) (156)

`profiler-cli load` without PROFILER_CLI_SESSION_DIR fails in the agent sandbox with EPERM creating ~/.profiler-cli, and the next command then says `Unknown session`. Agents must pass PROFILER_CLI_SESSION_DIR on every call; the brief never mentions it.

- **Question:** Load the test's profile into a session to start the investigation.
- **Command:** `PROFILER_CLI_SESSION_OWNER=browser_markup_load_01.js profiler-cli load <url> --session browser_markup_load_01.js-1`
- **Expected:** The profile loads.
- **Got:** `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, then `Unknown session`.
- **Suggestion:** Fall back to a writable default such as $TMPDIR/profiler-cli when the home directory is not writable.
- **Example from:** [mac/devtools/client/inspector/markup/test/browser_markup_load_01.js.files/profiler-cli-feedback.md](reports/mac/devtools/client/inspector/markup/test/browser_markup_load_01.js.files/profiler-cli-feedback.md)

## 4. A link's `marker=N` can't be mapped to a marker handle (153)

Mapping a link's `marker=N` to a handle is impossible directly: `load <link>` ignores `marker=`, `thread markers --list --json` rows lack `markerIndex`, and there is no `marker info --index`. The agent had to dump 284 handles through `marker info --json` and filter by markerIndex in Python.

- **Question:** Which marker does a link's `marker=39918` point to (one of ~13 identical CPU Use markers)?
- **Command:** `profiler-cli load '<url>' --session S`
- **Expected:** `load` prints the selected marker, e.g. "Selected marker: m-52 CPU Use t=272.945s", or `marker info --index 39918` exists.
- **Got:** Only the session status; `--list --json` rows carry no `markerIndex`, so name search can't identify the row.
- **Suggestion:** Have `load` name the URL's selected marker, add `markerIndex` to `--list --json` rows, or add `marker info --index N`.
- **Example from:** [browser/browser/components/extensions/test/xpcshell/test_ext_bookmarks.js.files/profiler-cli-feedback.md](reports/browser/browser/components/extensions/test/xpcshell/test_ext_bookmarks.js.files/profiler-cli-feedback.md)

## 5. Default thread after load is not the parent main thread (131)

After `load` of a raw Taskcluster per-test profile, the selected thread was t-20 (GeckoMain, Privileged Content), not parent t-0. The following `thread markers --category Test --search` printed "No markers match" with no hint that the Test markers live on t-0, reading as "the test left no log".

- **Question:** What does the test log (Test-category markers) of browser_preferences.js say in this profile?
- **Command:** `profiler-cli load <url> --session browser_preferences.js-3, then profiler-cli thread markers --category Test --search browser_preferences --list --limit 0 --session browser_preferences.js-3`
- **Expected:** Parent process GeckoMain (t-0) selected after load, as for the other CI per-test profiles, so the test log shows.
- **Got:** `t-20 (GeckoMain, Privileged Content)` selected; the search printed "No markers match the specified filters".
- **Suggestion:** Default to the parent GeckoMain for mochitest profiles, or when a --category Test search is empty on a non-parent thread, say which thread holds Test markers.
- **Example from:** [intermittent/browser/components/profiles/tests/browser/browser_preferences.js.files/profiler-cli-feedback.md](reports/intermittent/browser/components/profiles/tests/browser/browser_preferences.js.files/profiler-cli-feedback.md)

## 6. IPC marker rows show no message type, direction or peer (120)

`thread markers --search messageType:PRemoteDecoder --list` rows show only `IPCOut`/`IPCIn` and a duration; message type, direction and seqno are only in `--json`, so finding the request that never got a reply needed a Python script pairing Msg_Decode/Reply_Decode by seqno.

- **Question:** Which IPC request never got its reply?
- **Command:** `profiler-cli thread markers --search messageType:PRemoteDecoder --list --limit 0 --session <s>`
- **Expected:** Each IPC row showing its message type, direction and ideally seqno, so an unanswered Msg_Decode can be spotted.
- **Got:** Rows only say `IPCOut` / `IPCIn` with a duration.
- **Suggestion:** Show messageType in `--list` rows for IPC markers; add a way to list unreplied requests (e.g. `--unreplied`) or round-trip latency.
- **Example from:** [intermittent/dom/media/test/test_closing_connections.html.files/profiler-cli-feedback.md](reports/intermittent/dom/media/test/test_closing_connections.html.files/profiler-cli-feedback.md)

## 7. `--group-by` gives only counts, no time range or stats per group (116)

`thread markers --group-by field:innerWindowID` prints only a count and example handles per group, so it can't show which window has been ticking `RefreshDriverTick waiting for paint` since when.

- **Question:** When did each group of RefreshDriverTick markers (per innerWindowID) start and end?
- **Command:** `profiler-cli thread markers --search "RefreshDriverTick waiting for paint" --group-by field:innerWindowID`
- **Expected:** Per group: count plus first and last marker time (and handles).
- **Got:** Count and example handles only.
- **Suggestion:** Add first/last start time (and duration stats) per group.
- **Example from:** [browser/browser/components/extensions/test/browser/browser_ext_port_disconnect_on_window_close.js.files/profiler-cli-feedback.md](reports/browser/browser/components/extensions/test/browser/browser_ext_port_disconnect_on_window_close.js.files/profiler-cli-feedback.md)

## 8. Sub-millisecond precision unavailable in text times (105)

`thread markers --list` and `marker info` round times to the millisecond even in a zoom a few ms wide, so child-thread IPCIn, ModuleEvaluation and InterruptCallback markers tens of microseconds apart all print `t=2.083s`/`t=2.084s` and cannot be ordered against the parent thread.

- **Question:** Did the child get the interrupt request before or after module evaluation began, and before the parent's IPCOut?
- **Command:** `profiler-cli thread markers --session S --list --limit 0`
- **Expected:** Timestamps precise enough to order events tens of microseconds apart (2.08298, 2.08376, 2.08386).
- **Got:** Every row printed as `t=2.083s`/`t=2.084s`; `marker info` prints `Time: 2.084s`.
- **Suggestion:** Print times with as many decimals as the zoom span needs (sub-ms when the zoom is a few ms wide).
- **Example from:** [browser/browser/components/aboutlogins/tests/browser/browser_aaa_eventTelemetry_run_first.js.files/profiler-cli-feedback.md](reports/browser/browser/components/aboutlogins/tests/browser/browser_aaa_eventTelemetry_run_first.js.files/profiler-cli-feedback.md)

## 9. `--list` rows omit identifying payload fields; no `--fields` option (100)

`thread markers --list` rows for DOMEvent show only `unload - document`, without `innerWindowID`, so the sidebar's about:blank can't be told from its new document. The ID is only in `--json` `data.innerWindowID`; no option adds columns.

- **Question:** Which document did each DOMEvent fire on?
- **Command:** `profiler-cli thread markers --session <s> --search "name:DOMEvent" --list --limit 0`
- **Expected:** Each row showing the event's `innerWindowID`.
- **Got:** Rows like `unload - document` with no window ID.
- **Suggestion:** Show identifying payload fields in rows, or add a `--fields innerWindowID` option.
- **Example from:** [browser/browser/components/extensions/test/browser/browser_ext_sidebarAction_context.js.files/profiler-cli-feedback.md](reports/browser/browser/components/extensions/test/browser/browser_ext_sidebarAction_context.js.files/profiler-cli-feedback.md)

## 10. Search doesn't match text as displayed in the row label (91)

`thread markers --search 'Idle (0)'` or `'priority: Idle'` finds 0 Runnable markers although the list prints them as `... - priority: Idle (0) task: ...`; the label is composed from fields and the searchable value is `priorityName:Idle`.

- **Question:** Which Runnable markers ran at Idle priority?
- **Command:** `profiler-cli thread markers --session <s> --search 'Idle (0)' --list`
- **Expected:** The Runnable markers printed as `priority: Idle (0)`.
- **Got:** 0 markers.
- **Suggestion:** Match bare terms against the printed label too, or say which fields were searched in the 'no markers' message.
- **Example from:** [mac/toolkit/mozapps/extensions/test/xpcshell/test_AddonRepository_cache_locale.js.files/profiler-cli-feedback.md](reports/mac/toolkit/mozapps/extensions/test/xpcshell/test_AddonRepository_cache_locale.js.files/profiler-cli-feedback.md)

## 11. Payload time fields use a different time base than printed times (89)

`marker info --json` on an IPCIn shows `start` 3431.52 (t=3.432s), but raw `startTime`/`sendStartTime` are 3441.75, shifted by rootRange.start (10.23 ms), unlabelled. `context.currentViewRange` uses the shifted base while `zoom push` takes the unshifted one. A report misread a 2.5 ms gap as 12 ms.

- **Question:** When was this IPC reply sent, on the same clock as everything else?
- **Command:** `profiler-cli marker info m-1065 --json --session <s>`
- **Expected:** sendStartTime/recvEndTime on the same time base as `start` and every printed t=, or labelled as a different base.
- **Got:** `start` 3431.52 but rawFields `startTime`/`sendStartTime` 3441.75, 10.23 ms later, with no label.
- **Suggestion:** Convert payload time fields to the displayed base (t= seconds), or label them as raw with the offset.
- **Example from:** [intermittent/browser/components/preferences/tests/telemetry/browser_usage_telemetry_support_link.js.files/profiler-cli-feedback.md](reports/intermittent/browser/components/preferences/tests/telemetry/browser_usage_telemetry_support_link.js.files/profiler-cli-feedback.md)

## 12. `field:value` search is substring-only; no exact match (84)

`thread markers --search 'eventType:scroll,...'` does substring matching on field values, so `eventType:scroll` matched `MozScrolledAreaChanged`. There is no exact-match syntax, burying the single real result among 49 false hits.

- **Question:** Did any `scroll` event fire?
- **Command:** `profiler-cli thread markers --search 'eventType:scroll,eventType:scrollend,eventType:click' --list --limit 0 --session <s>`
- **Expected:** Only DOMEvent markers whose eventType is exactly scroll, scrollend or click.
- **Got:** 50 markers, 49 of them `MozScrolledAreaChanged`; the one real answer (a `click`) was buried.
- **Suggestion:** Add an exact-match form such as `eventType:=scroll`.
- **Example from:** [devtools/devtools/client/inspector/markup/test/events/browser_markup_events-overflow.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/inspector/markup/test/events/browser_markup_events-overflow.js.files/profiler-cli-feedback.md)

## 13. Search doesn't match schema-less / raw payload fields (81)

`thread markers --search "innerWindowID:4294967338"` returned no markers, although `--group-by field:innerWindowID` on the same thread groups by that field and `marker info` shows it under "Fields (raw payload, no schema)". Raw schema-less payload fields are not searchable, so finding a window's DocumentLoad needed one-by-one `marker info`.

- **Question:** Which document does this stuck refresh driver's window (innerWindowID) belong to?
- **Command:** `profiler-cli thread markers --session <s> --search "innerWindowID:4294967338" --list`
- **Expected:** Every marker carrying that innerWindowID, e.g. the DocumentLoad and nsRefreshDriver markers of that window.
- **Got:** `No markers match the specified filters.`
- **Suggestion:** Make --search field:value (and bare terms) match raw/schema-less payload fields, as --group-by field: already does.
- **Example from:** [mac/browser/components/extensions/test/browser/browser_ext_webNavigation_urlbar_transitions.js.files/profiler-cli-feedback.md](reports/mac/browser/components/extensions/test/browser/browser_ext_webNavigation_urlbar_transitions.js.files/profiler-cli-feedback.md)

## 14. Pairing markers or measuring gaps between them needs a script (79)

`thread markers --search "RefreshDriverTick waiting for paint"` Frequency Analysis reports `max=3.682s` interval but not where it is; finding whether the series paused during the vsync wait required dumping 12,583 markers with `--json` and sorting gaps in a script.

- **Question:** Did the leaked window's RefreshDriverTick waiting for paint markers ever pause during a vsync wait?
- **Command:** `profiler-cli thread markers --search "RefreshDriverTick waiting for paint" --session browser-review-browser_ext_runtime_onPerformanceWarning.js-1`
- **Expected:** The max interval to say where it is (start time, or the two marker handles).
- **Got:** Only the duration (`max=3.682s`).
- **Suggestion:** List the N longest gaps with start times/handles in the frequency analysis, or add a delta-to-previous column in `--list`.
- **Example from:** [browser/browser/components/extensions/test/browser/browser_ext_runtime_onPerformanceWarning.js.files/profiler-cli-feedback.md](reports/browser/browser/components/extensions/test/browser/browser_ext_runtime_onPerformanceWarning.js.files/profiler-cli-feedback.md)

## 15. `--session` rejected before the subcommand (77)

`--session` placed before the subcommand is rejected as an unknown option, although every call needs it and it looks global.

- **Question:** Basic profile overview for the loaded session.
- **Command:** `profiler-cli --session review-browser_console_clear_cache.js-1 profile info`
- **Expected:** The global session option accepted in any position.
- **Got:** `error: unknown option '--session' (Did you mean --version?)`.
- **Suggestion:** Accept `--session` as a global option before the subcommand.
- **Example from:** [devtools/devtools/client/webconsole/test/browser/browser_console_clear_cache.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/webconsole/test/browser/browser_console_clear_cache.js.files/profiler-cli-feedback.md)

## 16. profile-link.py refuses sessions loaded from a profiler.firefox.com link (72)

`profile-link.py` refuses to build a link for a session loaded from a report's `profiler.firefox.com/from-url/...` link (as review-brief.md instructs), although the from-url path wraps the raw artifact URL. Reviewers must stop and reload the 0.6 GB Taskcluster URL, and handles change.

- **Question:** Can I link a new observation found while reviewing, from the session I already loaded?
- **Command:** `python3 profile-link.py --session s --marker m-4428`
- **Expected:** A link, since the review brief says to check links by loading them.
- **Got:** "A link needs the raw artifact URL: load the Taskcluster URL itself"
- **Suggestion:** Have profile-link.py extract the artifact URL from the `from-url/` path, or expose it via `profiler-cli status --json`.
- **Example from:** [devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_census-tree-node-02.js.files/profiler-cli-feedback.md](reports/devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_census-tree-node-02.js.files/profiler-cli-feedback.md)

## 17. Can't filter or group markers by a frame in their stack (68)

`thread markers --search` matches only name/category/payload, never stack frames. Finding which SetNeedStyleFlush markers came from a given JS function took a shell loop of `marker stack m-N` over ~280 handles (about 30 s per profile, three profiles).

- **Question:** Which markers were emitted from JS function tabs.mjs!_updateCloseButtons?
- **Command:** `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0 --json --session <s>`
- **Expected:** Filter by a stack frame, e.g. `--stack-contains _updateCloseButtons`, or top JS frame shown in `--list` rows.
- **Got:** Only a check mark for "has stack"; stacks reachable only one `marker stack` call per handle.
- **Suggestion:** Add a `--stack-search <func>` filter to `thread markers` and/or a leaf JS frame column in `--list`.
- **Example from:** [intermittent/browser/base/content/test/browser-fullscreen/browser_bug1620341.js.files/profiler-cli-feedback.md](reports/intermittent/browser/base/content/test/browser-fullscreen/browser_bug1620341.js.files/profiler-cli-feedback.md)

## 18. innerWindowID can't be resolved to a page URL (68)

`marker info` shows only a raw `innerWindowID` with no page URL, no command lists the profile's `pages` table, and `--search innerWindowID:<id>` matches 0 markers. The agent had to curl the profile and read `processes[].pages` to find `moz-extension://…/popup.html`.

- **Question:** Which document does a marker's innerWindowID belong to?
- **Command:** `profiler-cli marker info m-914 --session browser_ext_commands_onChanged.js-1`
- **Expected:** The innerWindowID resolved to its page URL, or a command listing the pages table (URL, tabID, embedder).
- **Got:** Only `innerWindowID: 4294967331`; `--search 'innerWindowID:4294967331'` matched 0 markers.
- **Suggestion:** Print the page URL next to innerWindowID in `marker info`; add a `profile pages` command.
- **Example from:** [mac/browser/components/extensions/test/browser/browser_ext_commands_onChanged.js.files/profiler-cli-feedback.md](reports/mac/browser/components/extensions/test/browser/browser_ext_commands_onChanged.js.files/profiler-cli-feedback.md)

## 19. No end time shown for interval markers; no containment info (68)

In a 2 ms zoom, `thread markers --list` prints every row as `t=8.851s` with no end time, so one can't see that instant `HttpChannelChild::Cancel` (8851.22 ms) lies inside interval `ListenerBatch::DispatchTask` (8851.14-8851.30 ms).

- **Question:** Which runnable was running when this instant marker fired?
- **Command:** `profiler-cli thread markers --list --limit 0 --session <s>`
- **Expected:** Precise start/end times, enough to see the instant marker is inside the interval marker.
- **Got:** Every row printed as `t=8.851s`; no interval end times; order within a ms not by start time.
- **Suggestion:** Sub-ms times in narrow zooms, an end column, or an 'enclosing interval markers' line in `marker info`.
- **Example from:** [intermittent/dom/media/autoplay/test/mochitest/test_autoplay_policy.html.files/profiler-cli-feedback.md](reports/intermittent/dom/media/autoplay/test/mochitest/test_autoplay_policy.html.files/profiler-cli-feedback.md)

## 20. No filter for interval markers overlapping a time; no concurrency count (67)

`thread markers` has no way to count or filter interval markers overlapping a time. After `zoom push <test marker>`, `--search name:test --list` gave 261 rows incl. SKIPs and tests merely starting/ending inside, which a report misread as "about 20 other tests".

- **Question:** How many other tests ran alongside this one?
- **Command:** `profiler-cli thread markers --session <s> --search "name:test" --list --limit 0`
- **Expected:** Overlapping test markers summarized: concurrency (how many at once) and total, excluding SKIP.
- **Got:** 261 rows, including SKIPs and tests that only started or ended in the span; a script found 10 concurrent throughout.
- **Suggestion:** For interval markers, add a "concurrent: min/max" line to aggregate stats, or an `--overlapping <t>` filter.
- **Example from:** [browser/browser/components/urlbar/tests/unit/test_search_suggestions.js.files/profiler-cli-feedback.md](reports/browser/browser/components/urlbar/tests/unit/test_search_suggestions.js.files/profiler-cli-feedback.md)

## 21. Log marker Message/Level shown as "(empty)" or string index (63)

Mochitest INFO Log markers print `[(empty)] INFO: (empty)` in `thread markers --category Test --list` and `Level: (empty)` / `Message: (empty)` in `marker info`, while `--json` has the real text in `value` with `formattedValue: "(empty)"`. The test's own info() log needs one --json call per line.

- **Question:** What did the test's info() log lines say (e.g. "expecting a popup")?
- **Command:** `profiler-cli marker info m-67 --session <s>`
- **Expected:** `INFO  expecting a popup` (the test's info() log line).
- **Got:** `Level: (empty)` / `Message: (empty)`; list shows `[(empty)] INFO: (empty)`; `--json` has `"value": "expecting a popup"`, `"formattedValue": "(empty)"`.
- **Suggestion:** Fix the formatter to print the Log marker's value for Level/Message in --list and marker info.
- **Example from:** [browser/browser/extensions/formautofill/test/mochitest/test_multi_locale_CA_address_form.html.files/profiler-cli-feedback.md](reports/browser/browser/extensions/formautofill/test/mochitest/test_multi_locale_CA_address_form.html.files/profiler-cli-feedback.md)

## 22. Zoom includes overlapping markers; no "started in range" option (62)

After `zoom push 10.318,10.340`, `thread markers --list` shows 80+ IPCOut/IPCIn rows that started at t=1.04s and last 11-40 s before anything that happened in the 20 ms window; no option restricts to markers starting in the range.

- **Question:** What happened on the main thread in the 20 ms around this dragstart?
- **Command:** `profiler-cli thread markers --search "-name:Runnable,-name:Preference Read" --list --limit 200`
- **Expected:** The markers that start in the zoomed range, in order.
- **Got:** 80+ rows of IPCOut/IPCIn markers that started at t=1.04s and last 11-40 s, listed before anything in the window.
- **Suggestion:** Add an option such as `--starts-in-view` to list only markers starting inside the zoomed range.
- **Example from:** [mac/browser/components/customizableui/test/browser_892956_destroyWidget_defaultPlacements.js.files/profiler-cli-feedback.md](reports/mac/browser/components/customizableui/test/browser_892956_destroyWidget_defaultPlacements.js.files/profiler-cli-feedback.md)

## 23. `--json` key names differ between commands, or the handle is missing (54)

`marker info m-4 m-6 m-5 --json` returns records without a `handle` field, so multi-handle results can only be matched back to the requested handles by order.

- **Question:** Which JSON record corresponds to which requested marker handle?
- **Command:** `profiler-cli marker info m-4 m-6 m-5 --session <s> --json`
- **Expected:** Each record names its handle.
- **Got:** `handle` is absent (None); records have to be matched by order.
- **Suggestion:** Include `handle` in every `marker info --json` record, with the same key names as `thread markers --json`.
- **Example from:** [devtools/devtools/server/tests/xpcshell/test_pauselifetime-04.js.files/profiler-cli-feedback.md](reports/devtools/devtools/server/tests/xpcshell/test_pauselifetime-04.js.files/profiler-cli-feedback.md)

## 24. `zoom push` silently misreads minute formats the tool prints (51)

`zoom push 23m5s,23m32s`, using the time format `thread markers` prints, is silently read as 23 s to 23 s, a zero-length range; the next query says `No markers match`, which looks like an empty window.

- **Question:** What did the harness log between the manifest's last test and the LeakSanitizer report?
- **Command:** `profiler-cli zoom push 23m5s,23m32s --session <s>`
- **Expected:** A 27 s zoom from 23m5s to 23m32s, or an error.
- **Got:** `Pushed view range: ts-1 (23s) to ts-1 (23s) (duration: 0s)`, then `No markers match`, no warning.
- **Suggestion:** Accept the `XmYs` format the tool prints, or reject it with an error.
- **Example from:** [devtools/devtools/client/webconsole/test/browser/browser_console_nsiconsolemessage.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/webconsole/test/browser/browser_console_nsiconsolemessage.js.files/profiler-cli-feedback.md)

## 25. `marker info --json` shape changes with the number of handles (50)

`marker info --json` returns a bare marker object for one handle but `{type, requested, markers: [...], errors, context}` for several, so scripts written for one shape break on the other.

- **Question:** Does each report link's `markerIndex` match, for one link or several?
- **Command:** `profiler-cli marker info m-9 --json`
- **Expected:** One shape, regardless of handle count.
- **Got:** One handle gives a bare marker object; several give `{type, requested, markers: [...], errors, context}`. "My script broke twice."
- **Suggestion:** Always return `{markers: [...]}`.
- **Example from:** [devtools/devtools/client/inspector/markup/test/browser_markup_load_01.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/inspector/markup/test/browser_markup_load_01.js.files/profiler-cli-feedback.md)

## 26. Long marker labels truncated so the relevant part is never shown (50)

`thread markers --search launch_application --list` cuts each ~4 kB message at terminal width, so `_TEST_NAME` near the end is never shown; `marker info` shows it only for known handles. Needed `--list --json` plus a regex.

- **Question:** Which XpcshellTestRunnerService$iN and _TEST_NAME did each launch_application marker have?
- **Command:** `profiler-cli thread markers --search launch_application --list --limit 0`
- **Expected:** A way to see the full message, or the matched part of it.
- **Got:** Each message truncated at terminal width; `_TEST_NAME` never visible.
- **Suggestion:** A `--full-messages` flag on `thread markers --list`, or print the fragment of a long label around the `--search` match.
- **Example from:** [devtools/devtools/shared/tests/xpcshell/test_fetch-resource.js.files/profiler-cli-feedback.md](reports/devtools/devtools/shared/tests/xpcshell/test_fetch-resource.js.files/profiler-cli-feedback.md)

## 27. Daemon dies silently (on large loads or mid-session) (50)

`load` of a 113 MB gzipped per-test profile with a raised timeout returns after ~108 s with no error; the daemon has died (likely OOM) and its log ends at `Fetching profile from ...`. Neither client nor log explains why.

- **Question:** Load this per-test profile of a browser_tab_groups.js timeout.
- **Command:** `profiler-cli load <url> --session browser_tab_groups.js.timeout-1`
- **Expected:** The profile loads (like 18 others of 97-125 MB), or a clear error.
- **Got:** Only `Loading profile from ...`, then return; next command says `Unknown session`. Daemon log ends at `Fetching profile from ...`.
- **Suggestion:** Detect daemon death during load, exit non-zero, and log the exit reason (signal/OOM) in the daemon log.
- **Example from:** [mac/browser/components/tabbrowser/test/browser/tabs/browser_tab_groups.js.timeout.files/profiler-cli-feedback.md](reports/mac/browser/components/tabbrowser/test/browser/tabs/browser_tab_groups.js.timeout.files/profiler-cli-feedback.md)

## 28. JSON field layout inconsistent (empty fields, rawFields, missing duration) (50)

`marker info --json` on Network markers returns empty `fields`; `responseStatus`, `URI`, `redirectType` are only in `rawFields`, contrary to the guide saying payload keys are in `fields[]`.

- **Question:** What was the response status and URL of this Network marker?
- **Command:** `profiler-cli marker info m-44 m-45 --json --session review-browser_net_copy_headers.js-3`
- **Expected:** Payload keys in `fields[]`, as the guide describes.
- **Got:** `fields` empty for Network markers ("raw payload, no schema"); keys only in `rawFields`.
- **Suggestion:** Populate `fields` from the raw payload, or document `rawFields` for schema-less markers.
- **Example from:** [devtools/devtools/client/netmonitor/test/browser_net_copy_headers.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/netmonitor/test/browser_net_copy_headers.js.files/profiler-cli-feedback.md)

## 29. IPC phases and recipient handling time not shown (33)

`marker info` on an IPCIn marker shows only `Time: 55.954s - 55.954s (0s)` plus raw `recvEndTime: 55945.931458` / `endTime` in another time base (~8.2 ms off). When the message arrived on the I/O thread vs when the main thread handled it must be computed by hand.

- **Question:** When did this message reach the parent, as opposed to when the main thread ran it?
- **Command:** `profiler-cli marker info m-12147 --session <s>`
- **Expected:** recvEndTime converted to profile time, e.g. `Arrived: 55.9377s (16.8 ms before the main thread handled it)`.
- **Got:** `Time: 55.954s - 55.954s (0s)`; under "Other payload fields (no schema)": `endTime: 55962.686125`, `recvEndTime: 55945.931458`, raw ms in another base.
- **Suggestion:** Show IPC send/recv/handled times in profile time, with phase durations (send, I/O-thread receive, handled) in marker info.
- **Example from:** [mac/layout/style/test/test_img_src_causing_reflow.html.files/profiler-cli-feedback.md](reports/mac/layout/style/test/test_img_src_causing_reflow.html.files/profiler-cli-feedback.md)

## 30. Out-of-range or wrong-unit zoom ranges accepted silently (31)

`zoom push 244,312` on a 312 ms profile (values meant as ms) is accepted as seconds with no warning; every later query returns 0 markers without saying the range is outside the profile.

- **Question:** What happened between 244 ms and 312 ms of the profile?
- **Command:** `profiler-cli zoom push 244,312 --session S`
- **Expected:** A warning that 244 s to 312 s lies beyond the profile's 312 ms.
- **Got:** `View: ts>10l→ts>10y (1m8s)`, and every later query returned 0 markers with no explanation.
- **Suggestion:** Warn or error when the range falls outside the profile; accept an `ms` suffix.
- **Example from:** [intermittent/netwerk/test/unit/test_remove_invalid_first_party_partitioned_cookie.js.files/profiler-cli-feedback.md](reports/intermittent/netwerk/test/unit/test_remove_invalid_first_party_partitioned_cookie.js.files/profiler-cli-feedback.md)

## 31. No listing of individual samples with timestamps (31)

`thread functions --search loadStarterPrompts` (and `samples-top-down --search`) give only counts and percentages, never sample times, so placing the function before or after a marker took a zoom push/functions/pop loop over 10 ms buckets, about 20 round trips per question.

- **Question:** When was function X sampled, relative to a given marker?
- **Command:** `profiler-cli thread functions --search loadStarterPrompts --session ...`
- **Expected:** The times of the samples containing the function.
- **Got:** Only counts and percentages, with no time.
- **Suggestion:** A `--list`-style mode for samples matching `--search` with each sample's time, or first/last sample time per function.
- **Example from:** [browser/browser/components/aiwindow/ui/test/browser/browser_smartwindow_context_url.js.files/profiler-cli-feedback.md](reports/browser/browser/components/aiwindow/ui/test/browser/browser_smartwindow_context_url.js.files/profiler-cli-feedback.md)

## 32. `zoom clear`/`pop` leaves sample queries on the old range (29)

After `zoom push` then `zoom clear`, `thread samples`, `thread functions` and `samples-top-down` keep counting only the cleared zoom's samples while the header and `status` say "Full profile"; a `--search` then reports "No samples matched", reading as evidence of absence.

- **Question:** Does function X appear in samples over the full profile?
- **Command:** `profiler-cli zoom clear --session S`
- **Expected:** Samples commands cover the full profile (6335 running samples on this thread).
- **Got:** Header `View: Full profile`, but every samples command counted only the 2 samples of the cleared zoom (`thread functions` said "filtered from 20").
- **Suggestion:** Reset the sample range on `zoom clear`/`zoom pop`, matching the header.
- **Example from:** [devtools/devtools/client/jsonview/test/browser_jsonview_save_json.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/jsonview/test/browser_jsonview_save_json.js.files/profiler-cli-feedback.md)

## 33. Bare search terms match unexpected payload fields without saying which (28)

`thread markers --search file://` returns hundreds of IPCIn/IPCOut markers because their payload carries the peer process name `file:// Content (2/2)`; the output doesn't say which field matched, hiding the URL being sought.

- **Question:** Which navigation/load marker contains this file:// URL?
- **Command:** `profiler-cli thread markers --session <s> --search file:// --list --limit 50`
- **Expected:** Markers whose name or text contains `file://` (a URL in a load marker).
- **Got:** 370 and 358 hits, almost all `IPCIn` / `IPCOut`.
- **Suggestion:** Show which field matched each row.
- **Example from:** [mac/browser/base/content/test/browser-tabPrompts/browser_switchTabPermissionPrompt.js.files/profiler-cli-feedback.md](reports/mac/browser/base/content/test/browser-tabPrompts/browser_switchTabPermissionPrompt.js.files/profiler-cli-feedback.md)

## 34. Load timeout too short or misleading (28)

`load` of a large Taskcluster profile fails with a 60 s timeout while the daemon keeps loading; `status` then says "still loading", then times out, and the session works ~3 minutes later. The error reads as a failure.

- **Question:** Load this per-test profile into a session.
- **Command:** `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session epo-1`
- **Expected:** Either success or a failure that stopped the daemon.
- **Got:** `Error: Profile load timeout after 60000ms`; `status` answered "Profile still loading", then "Timed out after 30000ms waiting for the daemon".
- **Suggestion:** Wait until ready, or say loading continues in the background; raise the default timeout.
- **Example from:** [mac/devtools/client/inspector/rules/test/browser_rules_edit-property-order.js.files/profiler-cli-feedback.md](reports/mac/devtools/client/inspector/rules/test/browser_rules_edit-property-order.js.files/profiler-cli-feedback.md)

## 35. Hangs or daemon timeouts on very large threads or broad searches (27)

On a parent GeckoMain with 6.9M markers (99.8% a11y), an exclusion-only `thread markers --search` printed nothing and exited 0; the daemon had died (likely OOM) and the next command said `Unknown session`. The daemon log ends on `Received message: command`.

- **Question:** Which non-accessibility markers are on the parent main thread?
- **Command:** `profiler-cli thread markers --session browser-recent_activity-1 --search "-category:Accessibility,-name:Preference Read,-name:~ScriptLoadRequest,-name:DOMEvent" --list --limit 0`
- **Expected:** The non-a11y markers, or an error.
- **Got:** No output at all, exit 0; next command: `Unknown session`.
- **Suggestion:** Report daemon death to the client (non-zero exit, "daemon exited, likely out of memory"); warn upfront on threads with millions of markers.
- **Example from:** [browser/browser/components/preferences/tests/home/browser_homepage_firefox_home_recent_activity.js.files/profiler-cli-feedback.md](reports/browser/browser/components/preferences/tests/home/browser_homepage_firefox_home_recent_activity.js.files/profiler-cli-feedback.md)

## 36. Processes and threads shown by handle or pid, without names or times (26)

`profile info --all` prints process lifetimes as `ts-` handles (`[ts-5 → ts-a]`), not seconds, so finding which of 46 short-lived processes was alive at t=33.2s required a script over `--json` startTime/endTime.

- **Question:** Which child processes were alive at time t=33.2s?
- **Command:** `profiler-cli profile info --all --session <s>`
- **Expected:** Each process's start and end as times, e.g. `32.179s → 34.357s`.
- **Got:** `[ts-5 → ts-a]` timestamp handles, which cannot be compared by eye.
- **Suggestion:** Print seconds next to the ts handles, or add an `--alive-at <t>` filter.
- **Example from:** [intermittent/toolkit/components/ml/tests/browser/browser_ml_native.js.bug1987878.files/profiler-cli-feedback.md](reports/intermittent/toolkit/components/ml/tests/browser/browser_ml_native.js.bug1987878.files/profiler-cli-feedback.md)

## 37. No absolute wall-clock times; can't align two profiles (24)

Mapping a per-test profile marker onto the job's resource-usage profile timeline required running `profile meta --json` on both profiles and subtracting `startTime`s by hand; text `profile meta` rounds `Started:` to the ms and markers only show profile-relative times.

- **Question:** What time in the job's resource-usage profile is this per-test profile marker?
- **Command:** `profiler-cli profile meta --session <s> --json`
- **Expected:** A way to see a per-test marker's time on the resource-usage timeline, or the marker's absolute wall-clock time.
- **Got:** Only relative times; `Started:` rounded to the ms in text, so `--json` needed for both profiles and manual offset computation.
- **Suggestion:** Show absolute wall-clock time for markers, or an option to express times relative to another loaded profile.
- **Example from:** [browser/browser/components/tests/unit/test_distribution_bookmarks_first.js.files/profiler-cli-feedback.md](reports/browser/browser/components/tests/unit/test_distribution_bookmarks_first.js.files/profiler-cli-feedback.md)

## 38. Session socket path too long for long session names (24)

`load --session` with a session name following the brief's `<report name>-1` convention failed: socket path 105 bytes over a 103-byte limit. The error suggested setting PROFILER_CLI_SESSION_DIR to the exact directory already in use; the session name was the long part.

- **Question:** Load this test's per-test profile into a session named after the report.
- **Command:** `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli profiler-cli load <url> --session test_bug1293174_implicit_pointer_capture_for_touch_1.html-1`
- **Expected:** The session loads, or an error suggesting a shorter session name.
- **Got:** "The Unix socket path for this session is 105 bytes, over this platform's 103-byte limit ... Use a shorter session directory, for example: PROFILER_CLI_SESSION_DIR=<same dir>"
- **Suggestion:** Hash or truncate the socket file name (keep the id in metadata), check before downloading, or suggest a shorter --session when the name is the long part.
- **Example from:** [mac/dom/events/test/pointerevents/test_bug1293174_implicit_pointer_capture_for_touch_1.html.files/profiler-cli-feedback.md](reports/mac/dom/events/test/pointerevents/test_bug1293174_implicit_pointer_capture_for_touch_1.html.files/profiler-cli-feedback.md)

## 39. Stack capture time imprecise or unlabelled (23)

`marker stack` on a `setTimeout callback` marker prints `Captured at: 3m46s`, rounded to the second, so it can't be compared with the 54 ms `mousedown` it should fall inside.

- **Question:** Was this timer armed inside that 54 ms mousedown?
- **Command:** `profiler-cli marker stack m-1 --session S`
- **Expected:** Stack capture time at ms resolution.
- **Got:** `Captured at: 3m46s`.
- **Suggestion:** Print capture time in ms, labelled as when the stack was captured; also a Captured column in `--list`.
- **Example from:** [browser/browser/components/firefoxview/tests/browser/browser_opentabs_tab_indicators.js.files/profiler-cli-feedback.md](reports/browser/browser/components/firefoxview/tests/browser/browser_opentabs_tab_indicators.js.files/profiler-cli-feedback.md)

## 40. Bare `-term` exclusion silently matches nothing (22)

In `thread markers --search`, a bare `-term` exclusion (no `field:`) silently breaks the query: `-will retry` made the whole search return 0 markers with no warning, instead of excluding markers containing that text or erroring.

- **Question:** Which markers in this zoomed range remain once test, SKIP, CPU Use and "will retry" lines are excluded?
- **Command:** `profiler-cli thread markers --session <s> --search "-name:test,-name:SKIP,-name:CPU Use,-will retry" --list`
- **Expected:** Markers containing "will retry" excluded, or an error saying exclusion needs `field:value`.
- **Got:** "0 markers … No markers match the specified filters", with no warning.
- **Suggestion:** Support bare-text exclusions, or warn when a term parses as an exclusion without `field:`.
- **Example from:** [devtools/devtools/server/tests/xpcshell/test_breakpoint-19.js.files/profiler-cli-feedback.md](reports/devtools/devtools/server/tests/xpcshell/test_breakpoint-19.js.files/profiler-cli-feedback.md)

## 41. Multi-line or huge payloads printed in full in list output (21)

`profile markers --search 3des` prints 538 KB because each `Preference Read` marker of the mochitest PAC script pref (listing every test host) matches and is printed with its full multi-KB value, unwrapped.

- **Question:** Which network/page-load markers mention 3des.example.com?
- **Command:** `profiler-cli profile markers --search 3des --session browser-browser_aboutNetError.js-1`
- **Expected:** A handful of network/page-load markers, one line each.
- **Got:** 538 KB of output, dominated by full `network.proxy.autoconfig_url: data:text/plain,var knownOrigins = ...` values.
- **Suggestion:** Cut each row's payload to terminal width, or summarise huge payload matches.
- **Example from:** [browser/browser/base/content/test/browser-about/browser_aboutNetError.js.files/profiler-cli-feedback.md](reports/browser/browser/base/content/test/browser-about/browser_aboutNetError.js.files/profiler-cli-feedback.md)

## 42. Screenshots lack capture time/window, or are misreported (21)

`screenshots --at 609.070` returned 4 images, two of them a tab-note panel from an earlier test whose last screenshot was 13.5-13.8 s old, with no capture time or window id per image. It looked like a panel left open during the failure.

- **Question:** Which windows were actually on screen at t=609.07?
- **Command:** `profiler-cli screenshots --at 609.070 -o shots1/a --session browser-browser_tab_preview.js-1`
- **Expected:** The windows on screen at 609.07, or each image labelled with its capture time / window id.
- **Got:** 4 images, two a "Telemetry test note" panel from an earlier test whose last screenshot was 13.5-13.8 s long (m-4367, m-4383).
- **Suggestion:** Label each image with capture time and windowID, or omit windows closed or stale at t.
- **Example from:** [browser/browser/components/tabbrowser/test/browser/tabs/browser_tab_preview.js.files/profiler-cli-feedback.md](reports/browser/browser/components/tabbrowser/test/browser/tabs/browser_tab_preview.js.files/profiler-cli-feedback.md)

## 43. Sample views don't show sample counts or idle split (20)

`thread samples --include-idle` after `zoom push 3.887,4.222` prints `7 running samples` on both GeckoMain (none idle) and a DOM Worker (all 7 idle); the original report misread this as mostly idle, when the sampler only took 7 samples in 335 ms.

- **Question:** Was the thread busy or idle in this 335 ms range, and how many samples should it have?
- **Command:** `profiler-cli thread samples --include-idle`
- **Expected:** Total samples, idle count and sample spacing, e.g. "7 samples (0 idle), median spacing 56 ms (configured 10 ms)".
- **Got:** `Categories (7 running samples)` on both threads, although all 7 were Idle on the DOM Worker.
- **Suggestion:** Print total, idle and running sample counts separately, plus actual vs configured sampling interval.
- **Example from:** [browser/browser/components/urlbar/tests/unit/test_providerHeuristicFallback.js.files/profiler-cli-feedback.md](reports/browser/browser/components/urlbar/tests/unit/test_providerHeuristicFallback.js.files/profiler-cli-feedback.md)

## 44. `marker stack` shows only `unknown!null` for markers flagged as having a stack (20)

C++ warning markers are shown with ✓ (has stack) in `thread markers --list`, but `marker stack` on them prints only `[1] unknown!null`, the same for every such marker in the profile.

- **Question:** Which call site's `StrongWorkerRef::Create` was refused?
- **Command:** `profiler-cli marker stack m-1 --session review-2064988-1`
- **Expected:** Either a stack, or no ✓ in the list's "has stack trace" column.
- **Got:** The list shows ✓, but `marker stack` prints a single frame, `[1] unknown!null`.
- **Suggestion:** Show no ✓ (or "no usable stack") when the stack resolves to a single unknown frame.
- **Example from:** [intermittent/bugs/2064988.files/profiler-cli-feedback.md](reports/intermittent/bugs/2064988.files/profiler-cli-feedback.md)

## 45. Runnable dispatch origin not shown (20)

`marker info` on a Runnable shows its task id but not the TaskController::AddTask that queued it. Searching the id returns dozens of AddTask/Runnable markers because ids are addresses reused across the profile; the dispatch had to be picked by hand.

- **Question:** When was this runnable (nsXULPopupShownEvent, task 047f822c3609c9e10) dispatched?
- **Command:** `profiler-cli thread markers --search "047f822c3609c9e10" --list --limit 0`
- **Expected:** `marker info` on the Runnable naming its dispatching AddTask (same flow, the last before it), with time and index.
- **Got:** Dozens of AddTask/Runnable matches for the reused id; nothing links them.
- **Suggestion:** Show in `marker info` the latest preceding AddTask/PutEvent with the same flow id, or add a `--flow <id>` filter.
- **Example from:** [browser/browser/components/asrouter/tests/browser/browser_asrouter_cfr.js.files/profiler-cli-feedback.md](reports/browser/browser/components/asrouter/tests/browser/browser_asrouter_cfr.js.files/profiler-cli-feedback.md)

## 46. Empty call tree printed without a "no samples" message (19)

`thread samples-top-down` after zooming to a 50 ms `Jank` marker prints only the header and `Top-Down Call Tree:`; nothing says the range holds 0 samples, so it looks cut off or filtered.

- **Question:** What was the thread running during this 50 ms Jank marker?
- **Command:** `profiler-cli thread samples-top-down --limit 60 --session test_h2_unclean_shutdown_retry.js-3`
- **Expected:** A line saying the range holds 0 samples (or N idle samples excluded).
- **Got:** The header, "Top-Down Call Tree:", and nothing else.
- **Suggestion:** Print "0 samples in this range (sampling interval N ms)".
- **Example from:** [intermittent/netwerk/test/unit/test_h2_unclean_shutdown_retry.js.files/profiler-cli-feedback.md](reports/intermittent/netwerk/test/unit/test_h2_unclean_shutdown_retry.js.files/profiler-cli-feedback.md)

## 47. Negative durations and cross-process clock skew not flagged (17)

IPC markers on a content thread show negative durations in `--group-by` and `--list`, with no note about cross-process clock skew, making `--min-duration` filters on IPC unreliable.

- **Question:** Which IPC messages did this content process receive, and how long did they take?
- **Command:** `profiler-cli thread markers --search IPC --group-by field:messageType --session browser_test_select_zoom.js-1`
- **Expected:** Non-negative durations, or a note that the two processes' clocks disagree.
- **Got:** `PBrowser::Msg_RealTouchMoveEvent: 9 markers (avg=-55,686ns, max=-44,213ns)`; IPCIn rows listed with `-31,005ns` durations.
- **Suggestion:** Flag negative IPC durations and report the estimated per-process clock offset.
- **Example from:** [mac/gfx/layers/apz/test/mochitest/browser_test_select_zoom.js.files/profiler-cli-feedback.md](reports/mac/gfx/layers/apz/test/mochitest/browser_test_select_zoom.js.files/profiler-cli-feedback.md)

## 48. No quoting for `,` or `:` inside a search term (17)

`thread markers --search` cannot match a log line containing a comma: the comma is always the OR separator and cannot be quoted or escaped, so an exact-phrase search for a MOZ_LOG line returned 1189 markers.

- **Question:** Which marker carries this exact log line?
- **Command:** `profiler-cli thread markers --session <s> --search "Sending DataChannel open ack, channel 7c7b04272bc0" --list`
- **Expected:** The single output marker whose message contains that phrase.
- **Got:** 1189 markers, because the comma splits the search into two OR'd terms.
- **Suggestion:** Allow escaping/quoting a comma (and colon), or add an `--exact` flag.
- **Example from:** [intermittent/testing/web-platform/tests/webrtc/RTCDataChannel-close.html.files/profiler-cli-feedback.md](reports/intermittent/testing/web-platform/tests/webrtc/RTCDataChannel-close.html.files/profiler-cli-feedback.md)

## 49. No cross-thread chronological marker list (16)

To see a parent poll, a child's IPC send and the parent's receive in one ordered list, the agent scripted `thread select` + `thread markers --list --json` per thread and merged by start. `profile markers --search` can't be restricted to chosen threads and zoom as one flat chronological list.

- **Question:** Interleave markers from several threads chronologically: did the poll run before or after the IPC message arrived?
- **Command:** `profiler-cli thread markers --search X --list --json (per thread, merged by a script)`
- **Expected:** One flat time-sorted list across selected threads, honouring zoom.
- **Got:** No such view; `profile markers` sweeps all threads without a single chronological list.
- **Suggestion:** Support `--thread t-0,t-57,t-58 --list` (on `profile markers` or `thread markers`) merged by start time, honouring zoom.
- **Example from:** [intermittent/devtools/shared/commands/resource/tests/browser_resources_reflows.js.files/profiler-cli-feedback.md](reports/intermittent/devtools/shared/commands/resource/tests/browser_resources_reflows.js.files/profiler-cli-feedback.md)

## 50. CPU usage figures inconsistent or misleading (16)

`thread list` CPU column shows 211.347ms for t-58 (same in `profile info`), while its `Thread CPU use` marker says 6,738ms and `thread info` says 100% over 10.1 s; pid 8992 shows 470 ms vs 8,281 ms.

- **Question:** How much CPU did this process's main thread use?
- **Command:** `profiler-cli thread list --session <s>`
- **Expected:** About 6.7 s for t-58, matching `Thread CPU use` and `thread info`.
- **Got:** `211.347ms` for t-58; 470 ms against 8,281 ms for pid 8992.
- **Example from:** [browser/browser/components/aiwindow/models/tests/browser/browser_search_the_web.js.files/profiler-cli-feedback.md](reports/browser/browser/components/aiwindow/models/tests/browser/browser_search_the_web.js.files/profiler-cli-feedback.md)

## 51. `thread network` lacks timestamps, method, connection info; URL end cut (15)

`thread network` truncates long URLs at the end, cutting off the query string that distinguishes requests to the same path.

- **Question:** Which request is the page's `?sw-intercepted-resource` fetch, and which is the page itself?
- **Command:** `profiler-cli thread network --sort start --limit 0 --session <s>`
- **Expected:** URLs distinguishable by their query string.
- **Got:** `https://example.org/browser/browser/components/enterprisepolicies/tests/browser/sitepolicies_sw_f...`: the discriminating end is cut.
- **Suggestion:** Middle-elide URLs, keeping host, last path segment and query.
- **Example from:** [intermittent/browser/components/enterprisepolicies/tests/browser/browser_policy_sitepolicies_serviceworkers.js.bug2064102.files/profiler-cli-feedback.md](reports/intermittent/browser/components/enterprisepolicies/tests/browser/browser_policy_sitepolicies_serviceworkers.js.bug2064102.files/profiler-cli-feedback.md)

## 52. `thread network` hides failure status (14)

`thread network` summary reported `171 completed requests ... 0 failed` though 20 requests ended with NS_ERROR_NET_RESET / NS_ERROR_NET_PARTIAL_TRANSFER / NS_ERROR_PROXY_CONNECTION_REFUSED; those rows show only `???  cache=Unresolved`. The requestStatus is only in `marker info`.

- **Question:** Which network requests failed, and with which error?
- **Command:** `profiler-cli thread network --session <s> --sort start --limit 0`
- **Expected:** Failed requests counted as failed in the summary and requestStatus shown on each row.
- **Got:** `171 completed requests ... 0 failed`; failed rows show `???  cache=Unresolved  duration=...`, no status.
- **Suggestion:** Count non-NS_OK requestStatus as failed and print the status on those rows.
- **Example from:** [mac/netwerk/test/unit/test_http2_proxy_concurrent_stream.js.files/profiler-cli-feedback.md](reports/mac/netwerk/test/unit/test_http2_proxy_concurrent_stream.js.files/profiler-cli-feedback.md)

## 53. No sampling-gap distribution for a thread (14)

No command reports the achieved sample rate or gap distribution. To detect sampler starvation the agent looped `zoom push` + `thread samples --include-idle --limit 1` over 15 ranges, grepping the sample count; `thread info` only gives the total and CPU activity over time.

- **Question:** Was the sampler starved, and when (samples per second over time)?
- **Command:** `profiler-cli thread samples --include-idle --limit 1`
- **Expected:** Sample count per time bucket, or achieved rate vs configured interval ("918 samples, 15/s against 100/s configured").
- **Got:** Only the total sample count, and "CPU activity over time", which counts CPU and not samples.
- **Suggestion:** Show sample rate per bucket next to CPU activity in `thread info`, and median/largest gap vs nominal interval.
- **Example from:** [intermittent/toolkit/components/nimbus/test/unit/test_TargetingContextRecorder.js.files/profiler-cli-feedback.md](reports/intermittent/toolkit/components/nimbus/test/unit/test_TargetingContextRecorder.js.files/profiler-cli-feedback.md)

## 54. No per-test summary view (14)

Listing tests in a per-test profile with status: `--search name:test` also matches the payload `name` key of every Text marker (2285 rows), and `--search TEST-START` matches nothing; the agent resorted to a path search piped to `rg`.

- **Question:** Which tests ran in this browser session, in order, with their status?
- **Command:** `profiler-cli thread markers --session <s> --category Test --search name:test --list --limit 0`
- **Expected:** One row per `test` marker (`PASS — <path>` / `FAIL — <path>`).
- **Got:** 2285 rows, since `name:` also matches the payload `name` key of Text markers.
- **Suggestion:** A `profile tests` view listing harness `test` markers with status and duration, or a marker-name-only filter.
- **Example from:** [browser/browser/components/enterprisepolicies/tests/browser/browser_policy_sitepolicies_serviceworkers.js.files/profiler-cli-feedback.md](reports/browser/browser/components/enterprisepolicies/tests/browser/browser_policy_sitepolicies_serviceworkers.js.files/profiler-cli-feedback.md)

## 55. Search misses Log marker message text (13)

`profile markers --search "Unable to restore focus"` finds nothing although a mochitest INFO Log marker's `message` holds that text; the formatted value is `(empty)`, which search seems to use.

- **Question:** What did the harness log for this test before it timed out?
- **Command:** `profiler-cli profile markers --search "Unable to restore focus" --session test_bug331959.html-3`
- **Expected:** The INFO marker whose message is `Error: Unable to restore focus, expect failures and timeouts.`
- **Got:** `No markers match the specified filters (searched 19 threads).`
- **Suggestion:** Search on the raw message value; fix the `(empty)` formatting of Log markers.
- **Example from:** [intermittent/dom/base/test/test_bug331959.html.files/profiler-cli-feedback.md](reports/intermittent/dom/base/test/test_bug331959.html.files/profiler-cli-feedback.md)

## 56. `profile info` CPU figures ignore zoom (13)

After `zoom push 30.125,47.06`, `profile info` still prints full-profile per-thread CPU totals under the zoomed header.

- **Question:** How much CPU did this thread use within the zoomed range?
- **Command:** `profiler-cli profile info --session <s>`
- **Expected:** Per-thread CPU ms for the zoomed range, since the header shows the zoomed view.
- **Got:** Full-profile totals (Renderer 5424.029ms), unchanged by the zoom.
- **Suggestion:** Honour the zoom, or label the figures as full-profile.
- **Example from:** [devtools/devtools/client/debugger/test/mochitest/browser_dbg-audiocontext.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/debugger/test/mochitest/browser_dbg-audiocontext.js.files/profiler-cli-feedback.md)

## 57. Group-by key without `field:` prefix silently yields "(no value)" (13)

`thread markers --group-by "name,innerWindowID"` puts all 11,633 markers in one "(no value)" group, which reads as "no window"; only `field:innerWindowID` works. No error or hint.

- **Question:** How many RefreshDriverTick waiting-for-paint markers belong to each innerWindowID?
- **Command:** `profiler-cli thread markers --search "RefreshDriverTick waiting for paint" --group-by "name,innerWindowID"`
- **Expected:** One row per innerWindowID, or an error saying the key must be `field:innerWindowID`.
- **Got:** Every marker (11,633) in a single "(no value)" group.
- **Suggestion:** Error or suggest `field:<key>` when a group-by key is unknown; warn when no marker has the key.
- **Example from:** [browser/browser/components/extensions/test/browser/browser_ext_reload_manifest_cache.js.files/profiler-cli-feedback.md](reports/browser/browser/components/extensions/test/browser/browser_ext_reload_manifest_cache.js.files/profiler-cli-feedback.md)

## 58. Comma in marker `--search` is OR, with no AND form (12)

In `thread markers --search "name:test,test_telemetry_sampling"`, the comma ORs the terms, so instead of that test's `test` markers it listed every `test` marker in the job (818 KB). There is no way to AND a name filter with a text term.

- **Question:** Which `test` markers (first run, replay, retry) belong to test_telemetry_sampling.js in a resource-usage profile?
- **Command:** `profiler-cli thread markers --search "name:test,test_telemetry_sampling" --list --limit 0`
- **Expected:** That test's `test` markers only.
- **Got:** Positive terms OR'd (as documented): every `test` marker in the job, 818 KB of output.
- **Suggestion:** Add an AND form (e.g. `+term`) or a separate --name filter.
- **Example from:** [mac/toolkit/components/translations/tests/unit/test_telemetry_sampling.js.files/profiler-cli-feedback.md](reports/mac/toolkit/components/translations/tests/unit/test_telemetry_sampling.js.files/profiler-cli-feedback.md)

## 59. Views don't flag that data covers only part of the profile (12)

`profile info`/`thread info` show `Full: 1m39s` with no warning that the parent main thread's samples and markers only cover 1m37s-1m39s (buffer overflowed by 7M a11y markers). The test log search then looked broken rather than truncated.

- **Question:** What time range does the parent main thread actually hold data for?
- **Command:** `profiler-cli profile info --session browser_settings_turn_on_scheduled_backups.js-1`
- **Expected:** A warning that the thread's samples/markers only cover 1m37s-1m39s because the buffer filled and older data was evicted.
- **Got:** `Full: 1m39s` everywhere, `This thread contains 244 samples and 7036534 markers`, and CPU activity for 1m37s-1m39s.
- **Suggestion:** Print each thread's first/last retained sample and marker time when much shorter than the profile, and name buffer overflow.
- **Example from:** [mac/browser/components/backup/tests/browser/browser_settings_turn_on_scheduled_backups.js.files/profiler-cli-feedback.md](reports/mac/browser/components/backup/tests/browser/browser_settings_turn_on_scheduled_backups.js.files/profiler-cli-feedback.md)

## 60. `profile info` doesn't surface missing or merged processes (11)

A hung content process (pid 268) was launched and talked to over IPC but is absent from `profile info`/`thread list`, and `profile info --all` shows lifetimes only as ts handles; the agent found it only by reading Subprocess Priority markers and IPC `otherPid` values by hand.

- **Question:** Which child processes did the parent know about but are absent from the profile?
- **Command:** `profiler-cli profile info --all`
- **Expected:** Process start/end in seconds, and a line listing pids seen in IPC/Subprocess Priority markers with no process in the profile.
- **Got:** `[ts-q → ts-qe]` handles only; pid 268 not mentioned at all.
- **Suggestion:** In `profile info`, list pids referenced by IPC or Subprocess Priority markers that have no process in the profile, and print seconds next to ts handles.
- **Example from:** [devtools/devtools/client/framework/test/browser_toolbox_broken_xml_frame.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/framework/test/browser_toolbox_broken_xml_frame.js.files/profiler-cli-feedback.md)

## 61. Ids and lParam formatted with thousands separators (10)

Marker text renders pids with thousands separators (`[Child 7,232: Main Thread]`); `thread markers --search "Child 7,232"` splits on the comma, and `7232` matches nothing in the rendered text.

- **Question:** Which DocShell/DOMWindow/assertion markers belong to content process 7232?
- **Command:** `profiler-cli thread markers --session browser.toml-1 --search "Child 7,232" --list --limit 0`
- **Expected:** The markers of content process 7232.
- **Got:** 441 unrelated markers (CPU Use, Memory, IO...): the term was split into `Child 7` OR `232`.
- **Suggestion:** Don't insert thousands separators into pids/ids, or allow quoting/escaping a comma in `--search`.
- **Example from:** [intermittent/browser/extensions/webcompat/tests/browser/browser.toml.files/profiler-cli-feedback.md](reports/intermittent/browser/extensions/webcompat/tests/browser/browser.toml.files/profiler-cli-feedback.md)

## 62. `zoom push` rejects a pair of marker handles (9)

`zoom push m-14,m-2` is rejected, although `ts-6,ts-12` works and `m-N` alone is accepted. The agent had to read both markers' times via `marker info --json` and zoom by seconds.

- **Question:** What happened during the browser restart between one test's crash marker and the next test's marker?
- **Command:** `profiler-cli zoom push m-14,m-2 --session …`
- **Expected:** Zoom from the start of m-14 to the end of m-2.
- **Got:** `Error: Invalid time value: "m-14". Expected timestamp name (ts-X), seconds (2.7), milliseconds (2700ms), or percentage (10%)`
- **Suggestion:** Accept `m-A,m-B` as start of A to end of B.
- **Example from:** [mac/toolkit/content/tests/widgets/test_videocontrols_onclickplay.html.files/profiler-cli-feedback.md](reports/mac/toolkit/content/tests/widgets/test_videocontrols_onclickplay.html.files/profiler-cli-feedback.md)

## 63. Samples/functions search results inconsistent across calls (9)

In the full view, `thread samples-bottom-up --search nss3` and `thread functions --search yy_` matched nothing; after a `zoom push`/`zoom pop` pair, the same commands in the same state matched `nss3.dll!yy_reduce` etc. Function count also changed (273 vs 890+).

- **Question:** Which nss3 functions appear in the samples?
- **Command:** `profiler-cli thread samples-bottom-up --include-idle --search nss3 --limit 5`
- **Expected:** The same result for the same command and session state.
- **Got:** First `No samples matched --search "nss3"` and `0 functions (filtered from 273)`; later matches under `GleanBoolean.testGetValue`.
- **Example from:** [mac/toolkit/components/telemetry/tests/unit/test_TelemetryEnvironment_search.js.files/profiler-cli-feedback.md](reports/mac/toolkit/components/telemetry/tests/unit/test_TelemetryEnvironment_search.js.files/profiler-cli-feedback.md)

## 64. URL `thread=` token can't be mapped to a thread handle (9)

A report's links use `thread=yh`, `yb`, `y7`, `0`, but `thread list` shows only t-N handles and `--thread` doesn't accept URL tokens, so the agent decoded the base-32 by hand (`yh` = t-81).

- **Question:** Which thread handle is this link's `thread=yh`?
- **Command:** `profiler-cli load '<url>&thread=yf'`
- **Expected:** `thread list` showing each thread's URL token, or `thread select yh` accepted.
- **Got:** Nothing; mapped by hand from the URL encoding (`yh` = 2*32+17 = t-81).
- **Suggestion:** Print the URL thread token in `thread list` and accept it in `--thread`/`thread select`.
- **Example from:** [browser/browser/base/content/test/browser-fullscreen/browser_fullscreen_window_open.js.files/profiler-cli-feedback.md](reports/browser/browser/base/content/test/browser-fullscreen/browser_fullscreen_window_open.js.files/profiler-cli-feedback.md)

## 65. `marker stack` accepts only one handle (9)

`marker stack` takes a single handle, unlike `marker info`, so getting stacks of 28 `LSan Leak` markers took 28 calls plus a shell loop and awk.

- **Question:** Through which entry point was each of the manifest's 28 LSan-leaked objects allocated?
- **Command:** `profiler-cli marker stack m-6 --session review-eager_eval_resolve-1`
- **Expected:** One call over a range or search, e.g. `marker stack m-6..m-38`, as `marker info` accepts.
- **Got:** `marker stack` takes a single handle, so 28 calls.
- **Suggestion:** Accept multiple handles/ranges in `marker stack`, or add a stack column to `thread markers --has-stack`.
- **Example from:** [devtools/devtools/client/webconsole/test/browser/browser_console_eager_eval_resolve.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/webconsole/test/browser/browser_console_eager_eval_resolve.js.files/profiler-cli-feedback.md)

## 66. `--list` rows not sorted by start time (8)

`thread markers --list` in a 1m15s profile is not strictly chronological: m-11 TEST-PASS appears between m-17 and m-18 with the same `t=1m14s` label, so list order cannot tell whether the unload came before the hello packet delivery.

- **Question:** In what order, to the millisecond, did these markers happen?
- **Command:** `profiler-cli thread markers --category Test --search browser_application_panel_open-links.js --list --limit 0 --session <id>`
- **Expected:** Rows in start-time order with ms-precision times (e.g. `t=74.409s`).
- **Got:** `t=1m14s` on every row, and m-11 TEST-PASS listed between m-17 and m-18.
- **Suggestion:** Sort `--list` rows strictly by start time and print millisecond precision.
- **Example from:** [devtools/devtools/client/application/test/browser/browser_application_panel_open-links.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/application/test/browser/browser_application_panel_open-links.js.files/profiler-cli-feedback.md)

## 67. Handle numbering changes per daemon and query (8)

After reloading a resource-usage profile and running a different `--search` than before, reused m-N handle numbers pointed at unrelated tests, so profile-link.py links were wrong. Nothing warned that handles depend on daemon and query.

- **Question:** Build profiler links for markers identified in an earlier session of the same profile.
- **Expected:** Handles stable for the same profile, or a warning when a reused handle now names a different marker.
- **Got:** The links pointed at unrelated tests, silently.
- **Suggestion:** Warn in profile-link.py / marker info when the marker's name differs from the expected one, or make handles stable per profile.
- **Example from:** [intermittent/browser/components/backup/tests/browser/browser_settings_turn_on_scheduled_backups.js.files/profiler-cli-feedback.md](reports/intermittent/browser/components/backup/tests/browser/browser_settings_turn_on_scheduled_backups.js.files/profiler-cli-feedback.md)

## 68. Documentation gaps about search, group-by and zoom semantics (8)

The guide doesn't say `--group-by` accepts `field:<payload key>`; the aggregate hint and `--help` only show `field:eventType`. The agent scripted over `--json` to count PVsync messages per destination pid, though `--group-by name,field:otherPid` already did it.

- **Question:** How many `PVsync::Msg_Notify` messages does the parent send to each pid?
- **Command:** `profiler-cli thread markers --search PVsync --session S`
- **Expected:** A documented way to group by a payload field such as `otherPid`.
- **Got:** Only "IPCOut 1087 / IPCIn 325" aggregates; the `--group-by` hint never mentions `field:<payload key>`.
- **Suggestion:** Document `field:<key>` grouping in the guide/hint and list the groupable payload keys of matched markers.
- **Example from:** [browser/browser/components/extensions/test/browser/browser_ext_webNavigation_getFrames.js.files/profiler-cli-feedback.md](reports/browser/browser/components/extensions/test/browser/browser_ext_webNavigation_getFrames.js.files/profiler-cli-feedback.md)

## 69. `marker info` refuses handle ranges over 256 (7)

`marker info m-802..m-1164` refused a 363-handle range, forcing chunks of 200 with `--json` plus a script to average `cpuPercent` for one test window (five calls per window).

- **Question:** What was the machine's CPU use while this test ran?
- **Command:** `profiler-cli marker info m-802..m-1164`
- **Expected:** Marker info for all markers in the range.
- **Got:** "covers 363 handles, more than the maximum of 256".
- **Suggestion:** Raise or remove the 256-handle cap (at least with `--json`), or offer a CPU Use summary so it isn't needed.
- **Example from:** [mac/browser/components/enterprisepolicies/tests/xpcshell/test_extensionsettings.js.files/profiler-cli-feedback.md](reports/mac/browser/components/enterprisepolicies/tests/xpcshell/test_extensionsettings.js.files/profiler-cli-feedback.md)

## 70. Failed load leaves the session registered (7)

A `load` that fails with a transient 503 leaves the session registered with a live daemon; re-running the same `load` is refused as "already running" and `status` just replays the load error until `profiler-cli stop`.

- **Question:** Can I retry loading this profile after a transient fetch failure?
- **Command:** `profiler-cli load <url> --session S`
- **Expected:** The failed load cleans up, or a retry under the same id replaces the dead session.
- **Got:** `Session S is already running. Stop it first or choose a different session id.`; `session list` still showed S.
- **Suggestion:** Remove the session when load fails, and retry a 5xx once automatically.
- **Example from:** [browser/browser/base/content/test/browser-about/browser_aboutSupport.js.files/profiler-cli-feedback.md](reports/browser/browser/base/content/test/browser-about/browser_aboutSupport.js.files/profiler-cli-feedback.md)

## 71. No collapsing of repeated markers or change-point view (7)

`thread markers --search RefreshDriverTick --list` prints 991 rows one per marker; there's no way to collapse runs of identical labels to see when a tick reason started and stopped.

- **Question:** From when to when did a refresh tick reason stay present?
- **Command:** `profiler-cli thread markers --search RefreshDriverTick --list --limit 0`
- **Expected:** Runs of identical labels, e.g. "t=16.851–23.518s, 401× Tick reasons: HasObservers (...)".
- **Got:** One row per marker, too long to read; needed `--json` and a script collapsing consecutive identical labels.
- **Suggestion:** A `--runs` option collapsing consecutive identical labels, or `--group-by label` with first/last timestamps.
- **Example from:** [mac/browser/components/customizableui/test/browser_884402_customize_from_overflow.js.files/profiler-cli-feedback.md](reports/mac/browser/components/customizableui/test/browser_884402_customize_from_overflow.js.files/profiler-cli-feedback.md)

## 72. Marker payload truncated even in `--json` (7)

`marker info m-354 --json` on a GCMajor returns `timings` with `"truncated": true`, cut after `minor_gc_number`, so the JSON can't be parsed for reason/state; GCSlice list rows have no description either.

- **Question:** Which state transition did each GC slice make, and for what reason?
- **Command:** `profiler-cli marker info m-354 --json`
- **Expected:** Full `timings` payload in JSON.
- **Got:** `timings` truncated (`"truncated": true`) after `minor_gc_number`.
- **Suggestion:** Don't truncate raw payload fields in `--json`; show `initial_state -> final_state` in GCSlice labels.
- **Example from:** [intermittent/browser/components/urlbar/tests/browser/browser_suppressFocusBorder.js.files/profiler-cli-feedback.md](reports/intermittent/browser/components/urlbar/tests/browser/browser_suppressFocusBorder.js.files/profiler-cli-feedback.md)

## 73. Session management commands misbehave (6)

`profiler-cli session stop <id>` falls through to `list` and fails with a misleading argument-count error instead of stopping the session or pointing to `profiler-cli stop <id>`.

- **Question:** Stop one named session.
- **Command:** `profiler-cli session stop review-test_objectgrips-08.js-1`
- **Expected:** Session stopped, or "unknown subcommand stop; use `profiler-cli stop <id>`".
- **Got:** `error: too many arguments for 'list'. Expected 0 arguments but got 2: stop, review-test_objectgrips-08.js-1.`
- **Suggestion:** Make `session stop <id>` an alias of `stop <id>`, or error pointing to it.
- **Example from:** [devtools/devtools/server/tests/xpcshell/test_objectgrips-08.js.files/profiler-cli-feedback.md](reports/devtools/devtools/server/tests/xpcshell/test_objectgrips-08.js.files/profiler-cli-feedback.md)

## 74. `--min-duration 0` drops instant markers (5)

`thread markers --min-duration 0 --list --json` in a 0.5 s zoom returned 123 markers instead of 210: all 87 instant markers (duration null), including the TEST-PASS / TEST-UNEXPECTED-FAIL being looked for, were silently dropped.

- **Question:** Every marker in a 0.5 s window, in order.
- **Command:** `profiler-cli thread markers --min-duration 0 --list --limit 0 --json --session <s>`
- **Expected:** `--min-duration 0` to be a no-op: every marker in the window.
- **Got:** 123 markers instead of 210; all 87 instant markers dropped, with no notice.
- **Suggestion:** Keep instant markers at --min-duration 0, or print "N instant markers excluded".
- **Example from:** [intermittent/layout/style/test/test_transitions_reversing_omta.html.files/profiler-cli-feedback.md](reports/intermittent/layout/style/test/test_transitions_reversing_omta.html.files/profiler-cli-feedback.md)

## 75. No awake-time vs CPU-time summary (5)

`thread info` shows CPU but not awake time, so telling whether a main thread was CPU-starved or waiting needs a script summing `Awake` marker durations against their `CPU Time` field (26.7 s awake vs 6.6 s CPU).

- **Question:** Was the test's main thread starved of CPU or waiting?
- **Command:** `profiler-cli thread markers --session <s> --search "name:Awake" --list --limit 0 --json`
- **Expected:** `thread info` printing awake time next to CPU time, plus the longest Awake stretches with their CPU share.
- **Got:** Only per-marker rows; the totals (26.7 s awake vs 6.6 s CPU) needed a Python script.
- **Suggestion:** Add awake time, CPU share while awake, and longest Awake stretches to `thread info` for the current view.
- **Example from:** [mac/toolkit/mozapps/extensions/test/xpcshell/test_installOrigins.js.files/profiler-cli-feedback.md](reports/mac/toolkit/mozapps/extensions/test/xpcshell/test_installOrigins.js.files/profiler-cli-feedback.md)

## 76. `samples-top-down` has no depth limit (5)

`thread samples-top-down` has no depth limit: `--max-depth` is rejected, and `--max-lines` caps nodes, not depth.

- **Question:** What does a shallow, depth-limited call tree under this function look like?
- **Command:** `profiler-cli thread samples-top-down --root-at f-396 --max-depth 12`
- **Expected:** A depth-limited tree.
- **Got:** `error: unknown option '--max-depth'`.
- **Suggestion:** Add a `--max-depth` option to call-tree commands.
- **Example from:** [intermittent/docshell/test/unit/test_URIFixup_info.js.files/profiler-cli-feedback.md](reports/intermittent/docshell/test/unit/test_URIFixup_info.js.files/profiler-cli-feedback.md)

## 77. Handle ranges include unrelated markers (5)

`marker info m-2..m-11` using the first and last handles of a filtered `--list` returned other markers, some not in that list, because list handles aren't in list order (m-49 sat between them), contrary to the guide's "consecutive list rows".

- **Question:** What are the details of the markers in these filtered list rows?
- **Command:** `profiler-cli marker info m-2..m-11 --json --session S`
- **Expected:** The markers of those list rows, as the guide says ranges cover consecutive list rows.
- **Got:** Other markers, some not from that list at all; the list's rows had m-49 between m-2 and m-11.
- **Suggestion:** Assign handles in list order, or make ranges follow the last list's rows.
- **Example from:** [browser/browser/components/aiwindow/ui/test/browser/browser_smartwindow_jump_to_bottom.js.files/profiler-cli-feedback.md](reports/browser/browser/components/aiwindow/ui/test/browser/browser_smartwindow_jump_to_bottom.js.files/profiler-cli-feedback.md)

## 78. `name:` filter also matches payload fields called `name` (4)

`thread markers --search "name:test"` returned 2,024 markers instead of the 33 `test` markers, because `name` is also a payload key on Text markers (TestUtils/BrowserTestUtils/task). No way to match the marker name only.

- **Question:** Which tests ran in this browser session, in order, with their status?
- **Command:** `profiler-cli thread markers --thread t-0 --search "name:test" --category Test --list --limit 0`
- **Expected:** The 33 markers named `test` (PASS/FAIL - <path>).
- **Got:** 2,024 markers, every TestUtils/BrowserTestUtils/task marker matched; `--group-by name` shows `test: 33 markers` but can't list them.
- **Suggestion:** Add an exact marker-name matcher (e.g. `markername:test`), or make `name:` match only the marker name.
- **Example from:** [mac/browser/components/extensions/test/browser/browser_ext_tab_runtimeConnect.js.files/profiler-cli-feedback.md](reports/mac/browser/components/extensions/test/browser/browser_ext_tab_runtimeConnect.js.files/profiler-cli-feedback.md)

## 79. `thread info` sample count ignores zoom (4)

After `zoom push 0.535,1.235`, `thread info` still prints the whole thread's sample count while the header shows the zoomed view; the in-range count is only in `thread samples --json` `categoryBreakdown.totalSamples`.

- **Question:** How many samples are in the zoomed range?
- **Command:** `profiler-cli thread info --session <s>`
- **Expected:** The sample count for the zoomed range (header: `View: ts-i→ts-z (700.00ms)`).
- **Got:** "This thread contains 35 samples" (whole thread); zoomed count was 23.
- **Suggestion:** Give the in-view sample count in `thread info` or the `thread samples` header.
- **Example from:** [mac/toolkit/crashreporter/test/unit/test_crashreporter_appmem.js.files/profiler-cli-feedback.md](reports/mac/toolkit/crashreporter/test/unit/test_crashreporter_appmem.js.files/profiler-cli-feedback.md)

## 80. JS frames lack line/column numbers (4)

`marker stack` shows JS frames with function names only, no line numbers, so it can't tell which of three call sites of helper `assertNotBadged` failed.

- **Question:** Which caller of the test helper `assertNotBadged` failed?
- **Command:** `profiler-cli marker stack m-23 --session browser-lifecycle-1`
- **Expected:** JS frames with line numbers, e.g. `test_selectable_profiles_lifecycle.js!test_SelectableProfileLifecycle:278`.
- **Got:** Function names only (`...!assertNotBadged`, `...!test_SelectableProfileLifecycle`).
- **Suggestion:** Print line/column for JS frames when the frame table has them, in text and `--json`.
- **Example from:** [browser/browser/components/profiles/tests/unit/test_selectable_profiles_lifecycle.js.files/profiler-cli-feedback.md](reports/browser/browser/components/profiles/tests/unit/test_selectable_profiles_lifecycle.js.files/profiler-cli-feedback.md)

## 81. `marker info --json` dumps full screenshot image data (3)

`marker info --json` on a CompositorScreenshot marker prints the full base64 JPEG in both `screenshot.url` and `screenshot.base64` (~15 KB), although only `markerIndex` and `threadHandle` were needed; `rawFields` already truncates the same data.

- **Question:** What is this screenshot marker's markerIndex and thread?
- **Command:** `profiler-cli marker info --session S m-11503 --json`
- **Expected:** Index, thread, times and fields, with image data truncated or left to `screenshots`.
- **Got:** Full base64 JPEG in `screenshot.url` and again in `screenshot.base64`, about 15 KB for one marker.
- **Suggestion:** Truncate `screenshot` like `rawFields` unless `--full`, or list markerIndex/thread in `screenshots` output.
- **Example from:** [browser/browser/components/downloads/test/browser/browser_download_spam_protection.js.files/profiler-cli-feedback.md](reports/browser/browser/components/downloads/test/browser/browser_download_spam_protection.js.files/profiler-cli-feedback.md)

## 82. No time filter on markers without a zoom (3)

`thread markers --list` has no `--from`/`--to` time filter, and `zoom push` needs an explicit end. Filtering the text output with awk on `t=` silently drops later markers, since strings compare wrongly (`t=16.563s` vs `t=2.8s`).

- **Question:** Which activity runnables ran after t=2.8 s, for the rest of the profile?
- **Command:** `thread markers --list --search ...`
- **Expected:** A simple way to list markers after a time without looking up the profile end.
- **Got:** String comparison in awk silently dropped later markers; `zoom push 2.8,<end>` works but needs the end time.
- **Suggestion:** Add `--from <t>`/`--to <t>` on `thread markers`, or accept `zoom push 2.8,end`.
- **Example from:** [devtools/devtools/client/netmonitor/src/har/test/browser_net_har_import.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/netmonitor/src/har/test/browser_net_har_import.js.files/profiler-cli-feedback.md)

## 83. `profile markers` lacks per-thread breakdown (3)

`profile markers --search` prints a total and an interleaved list of the first markers, with no per-thread counts or first/last times. Getting them needed `--json` and grouping by `threadHandle` in a script.

- **Question:** Which processes received `PVsync::Msg_Notify` during the vsync wait, how many times, from when to when?
- **Command:** `profiler-cli profile markers --search "PVsync::Msg_Notify" --session onCNT-1`
- **Expected:** A per-thread summary: thread, count, first, last.
- **Got:** "1419 markers across 3 of 33 threads", then an interleaved list of the first markers; no per-thread counts.
- **Suggestion:** Print a per-thread summary first, e.g. `t-32 WebExtensions: 472 IPCIn, 382.769..390.619`.
- **Example from:** [mac/browser/components/extensions/test/browser/browser_ext_webNavigation_onCreatedNavigationTarget.js.files/profiler-cli-feedback.md](reports/mac/browser/components/extensions/test/browser/browser_ext_webNavigation_onCreatedNavigationTarget.js.files/profiler-cli-feedback.md)

## 84. `profile logs` ignores zoom range (3)

`profile logs` ignores the zoom: after `zoom push 232.79,233.35` it printed all 2,750 entries from profile start under a zoomed header, stamped with wall-clock UTC only, so log lines couldn't be aligned with markers.

- **Question:** Which log entries fall within this 560 ms window, aligned with the markers?
- **Command:** `profiler-cli profile logs --limit 0 --session S`
- **Expected:** Only Log markers in the zoomed range, with profile-relative times.
- **Got:** All 2,750 entries (header `View: ts-zH→ts-zn (560ms)`), stamped `2026-09-21 01:57:59.616908935 UTC`.
- **Suggestion:** Apply the zoom range to `profile logs` and show profile-relative times.
- **Example from:** [intermittent/devtools/client/inspector/test/browser_inspector_picker-page-reload.js.files/profiler-cli-feedback.md](reports/intermittent/devtools/client/inspector/test/browser_inspector_picker-page-reload.js.files/profiler-cli-feedback.md)

## 85. Repeated `--search` keeps only the last (3)

Passing `--search` twice to `thread markers` silently applies only the last one, returning 48071 unfiltered markers instead of the intended filter.

- **Question:** Which TEST- markers appear, excluding Bailout markers?
- **Command:** `profiler-cli thread markers --search 'TEST-,Bailout' --search '-name:Bailout' --list --limit 0 --session <s>`
- **Expected:** Both filters ANDed, or an error saying `--search` can only be given once.
- **Got:** Only `-name:Bailout` applied: 48071 unfiltered markers (Preference Read, IPCIn...).
- **Suggestion:** AND repeated `--search` values, or error out.
- **Example from:** [mac/dom/canvas/test/webgl-conf/generated/test_2_conformance__misc__uninitialized-test.html.files/profiler-cli-feedback.md](reports/mac/dom/canvas/test/webgl-conf/generated/test_2_conformance__misc__uninitialized-test.html.files/profiler-cli-feedback.md)

## 86. `profile markers --list` rejected (3)

`profile markers ... --list` fails with `unknown option '--list'`, though the guide describes `profile markers` as giving the "same rows as `thread markers --list`", where --list is needed. Costs a call each time.

- **Question:** Which markers across threads have eventType pageshow?
- **Command:** `profiler-cli profile markers --search "eventType:pageshow" --list --limit 0 --session review-test_bug112564.xhtml-1`
- **Expected:** --list accepted or ignored, as the guide implies.
- **Got:** `error: unknown option '--list'`.
- **Suggestion:** Accept --list as a no-op on profile markers.
- **Example from:** [mac/docshell/test/chrome/test_bug112564.xhtml.files/profiler-cli-feedback.md](reports/mac/docshell/test/chrome/test_bug112564.xhtml.files/profiler-cli-feedback.md)

## 87. Non-printable characters not escaped (3)

`marker info m-23` prints C1 control characters (U+0081, U+0090) raw in text and `--json`, so the terminal drops them and a 6-code-point string looks like 4, hiding a windows-1252 decoding bug.

- **Question:** What are the exact code points in this TEST-UNEXPECTED-FAIL message?
- **Command:** `profiler-cli marker info m-23 --session irzh-1`
- **Expected:** Visible evidence of U+00E7 U+0081 U+00AB U+00E7 U+2039 U+0090.
- **Got:** C1 controls printed raw in text and JSON; terminal hides them.
- **Suggestion:** Escape non-printable characters in text output, or add a flag for it.
- **Example from:** [mac/toolkit/content/tests/widgets/test_image_recognition_zh.html.files/profiler-cli-feedback.md](reports/mac/toolkit/content/tests/widgets/test_image_recognition_zh.html.files/profiler-cli-feedback.md)

## 88. `thread info` reports an exited thread as still alive (2)

`thread info --thread t-323` says `Ended at: still alive at end of recording`, while `profile info --all --json` gives its process `endTime` 75185.4 ms.

- **Question:** Which processes and threads were alive during the stall?
- **Command:** `profiler-cli thread info --thread t-323 --session review-coirtp-1`
- **Expected:** An end time matching its process (endTime 75185.4 ms).
- **Got:** `Ended at: still alive at end of recording`.
- **Suggestion:** Use the process end time for threads whose process ended before the recording did.
- **Example from:** [mac/browser/components/resistfingerprinting/test/browser/browser_cross_origin_isolated_reduce_time_precision.js.files/profiler-cli-feedback.md](reports/mac/browser/components/resistfingerprinting/test/browser/browser_cross_origin_isolated_reduce_time_precision.js.files/profiler-cli-feedback.md)

## 89. `--json` flatMarkers description empty for Test markers (2)

`thread markers --list --json` returns an empty `description` for Test-schema `test` markers, while the text list shows `PASS — .../test_ext_background_early_shutdown.js`, so scripts get no test names.

- **Question:** Which tests were still running at t=X?
- **Command:** `profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json`
- **Expected:** Each flat marker carrying the text shown in the plain list.
- **Got:** `description` is empty for these Test-schema markers; had to cross-reference handles with the text list.
- **Suggestion:** Emit the same one-line description in `--json` as in text; maybe a `--running-at <t>` filter.
- **Example from:** [devtools/devtools/server/tests/xpcshell/test_stepping-14.js.files/profiler-cli-feedback.md](reports/devtools/devtools/server/tests/xpcshell/test_stepping-14.js.files/profiler-cli-feedback.md)

## 90. `--category` matching nothing gives no hint of valid categories (2)

`thread markers --category Test` on a resource-usage profile returns 0 markers silently; the test markers are in category Other and the thread only has Other, Tasks, Phases, but nothing hints at that.

- **Question:** What did the test log in this resource-usage profile?
- **Command:** `profiler-cli thread markers --session S --category Test --list --limit 0 --json`
- **Expected:** The `test` / INFO markers (subagent-brief.md suggests `--category Test`).
- **Got:** 0 markers, with no hint; the categories there are Other, Tasks, Phases.
- **Suggestion:** List the thread's available categories when a `--category` filter matches nothing.
- **Example from:** [devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_DominatorTreeNode_insert_01.js.files/profiler-cli-feedback.md](reports/devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_DominatorTreeNode_insert_01.js.files/profiler-cli-feedback.md)

## 91. First query right after `load` times out waiting for the daemon (2)

The first `thread markers` query right after `load` printed `Session started` failed with a 30 s daemon timeout; the same command worked on retry.

- **Question:** What did the test log say?
- **Command:** `profiler-cli thread markers --category Test --search split-orientation --list --limit 0 --json --session review-split-1`
- **Expected:** The marker list, or a message saying the daemon is still indexing.
- **Got:** `Error: Timed out after 30000ms waiting for the daemon on .../review-split-1.sock to answer.`
- **Suggestion:** Make `load` return only once the daemon can answer, or say the daemon is busy and a retry will work.
- **Example from:** [mac/devtools/client/inspector/test/browser_inspector_split-orientation.js.files/profiler-cli-feedback.md](reports/mac/devtools/client/inspector/test/browser_inspector_split-orientation.js.files/profiler-cli-feedback.md)

## 92. Comma in `--search` means OR, AND or literal depending on command (2)

`thread network --search 'a,b'` takes the comma literally and matches nothing, while `thread markers --search a,b` ORs. The "0 requests" result was first read as "the scripts were never loaded".

- **Question:** Were the test_worker.js and test_service_worker scripts loaded over the network?
- **Command:** `profiler-cli thread network --session <s> --search 'test_worker.js,test_service_worker' --limit 0`
- **Expected:** Both sets of requests, since comma is OR in thread markers --search.
- **Got:** `0 requests (filtered from 24)`.
- **Suggestion:** Use consistent comma semantics across commands, or say when the comma was taken literally.
- **Example from:** [devtools/devtools/shared/commands/resource/tests/browser_resources_console_messages_workers.js.files/profiler-cli-feedback.md](reports/devtools/devtools/shared/commands/resource/tests/browser_resources_console_messages_workers.js.files/profiler-cli-feedback.md)

## 93. `thread markers --list --json` returns the aggregate instead of rows (2)

`thread markers --list --json` returns the aggregated `byType` summary rather than the flat rows text `--list` shows, so reading marker start times needed `--json --top-n 100000` and flattening `.byType[].topMarkers[]` with jq.

- **Question:** In which order did these network markers happen, and how far apart?
- **Command:** `profiler-cli thread markers --session S --category Network --list --limit 0`
- **Expected:** `--list --json` returns a flat list of marker rows with `start`.
- **Got:** The `byType` aggregate instead of rows.
- **Suggestion:** Make `--list --json` emit the flat marker rows.
- **Example from:** [devtools/devtools/client/debugger/test/mochitest/browser_dbg-unselected-pause.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/debugger/test/mochitest/browser_dbg-unselected-pause.js.files/profiler-cli-feedback.md)

## 94. Thread header shows a different thread/process than `thread info` (2)

After loading a profiler link, the load status and every command header name t-57 with another process's name than `thread info` uses, so it looked as if the link opened the wrong process.

- **Question:** Which thread is selected after loading the report's link?
- **Command:** `profiler-cli load '<url>' --session <s>`
- **Expected:** The header to name t-57 as `thread info` does: `https://example.com (18/18)`.
- **Got:** `Selected thread: t-57 (GeckoMain, https://example.org (3/14))`, while `thread info` says `Name: https://example.com (18/18)`.
- **Suggestion:** Use the same thread/process name source in headers as `thread info` and `thread list`.
- **Example from:** [browser/browser/components/aiwindow/ui/test/browser/browser_smartformfill_tab_selector.js.files/profiler-cli-feedback.md](reports/browser/browser/components/aiwindow/ui/test/browser/browser_smartformfill_tab_selector.js.files/profiler-cli-feedback.md)

## 95. `profile info --json` gives every process the same startTime (2)

`profile info --all --json` reports the same `startTime` for every process, including ones launched minutes into the profile, while `endTime` looks right.

- **Question:** When did each content process start and end?
- **Command:** `profiler-cli profile info --all --json | jq '.processes[] | {name,startTime,endTime}'`
- **Expected:** Per-process startTime reflecting each process's actual launch.
- **Got:** `startTime` is 1116.9 ms for every one of the 56 processes; endTime looked right.
- **Example from:** [devtools/devtools/client/inspector/test/browser_inspector_inspect_node_contextmenu.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/inspector/test/browser_inspector_inspect_node_contextmenu.js.files/profiler-cli-feedback.md)

## 96. Screenshots are low resolution, with no crop option (2)

`profiler-cli screenshots` writes 350x259 thumbnails of a 1280x949 window with no crop/zoom; a ~70 px wide vertical tab strip was unreadable, and upscaling the whole frame with sips was the only workaround.

- **Question:** Was a vertical tab row drawn multiselected in this part of the window?
- **Command:** `profiler-cli screenshots --range 669.40,669.70 -o <dir> --session bsvcm-1`
- **Expected:** Enough resolution to tell whether the tab row was drawn multiselected.
- **Got:** 350x259 thumbnails of a 1280x949 window; no crop/zoom option.
- **Suggestion:** Add `--crop x,y,w,h` (window CSS pixels) and `--scale N` to write a region at full resolution.
- **Example from:** [mac/browser/components/tabbrowser/test/browser/tabs/browser_tab_splitview_contextmenu.js.files/profiler-cli-feedback.md](reports/mac/browser/components/tabbrowser/test/browser/tabs/browser_tab_splitview_contextmenu.js.files/profiler-cli-feedback.md)

## 97. No batch mode to query many profiles at once (2)

Classifying 27 failing jobs' resource-usage profiles by failure mode needed a shell loop of `load`, `thread markers --search`, `stop` per profile, grepping for three strings; about 15 minutes, with no batch mode.

- **Question:** Which failure mode is each of this test's 27 failing jobs in?
- **Command:** `profiler-cli thread markers --search "<test>.js" --list --limit 0`
- **Expected:** Per-profile match counts for several searches in one command.
- **Got:** One daemon load/query/stop per profile via `scan.sh`, about 15 minutes.
- **Suggestion:** A batch command, e.g. `profiler-cli markers-count --search A --search B <url>...`, printing per-profile counts without a daemon per profile.
- **Example from:** [mac/toolkit/mozapps/update/tests/unit_update_binary/marFailurePartialZucchiniBadAlloc.js.files/profiler-cli-feedback.md](reports/mac/toolkit/mozapps/update/tests/unit_update_binary/marFailurePartialZucchiniBadAlloc.js.files/profiler-cli-feedback.md)

## 98. No filter for markers inside or outside another search's intervals (2)

To check whether async statement errors fall inside Sqlite transactions, the agent ran two `thread markers --json` searches and joined them in Python; `thread markers` has no `--during-marker`/`--outside-marker` filter like the samples commands.

- **Question:** Is every notifyErrorOnCallingThread inside a 'waiting for clients' interval, and how many before/after SQLITE_BUSY?
- **Command:** `profiler-cli thread markers --search notifyErrorOnCallingThread --list --json`
- **Expected:** A way to filter markers by containment in another search's intervals.
- **Got:** Only flat lists; a script was needed to correlate the two sets.
- **Suggestion:** Add `--during-marker <search>` / `--outside-marker` to `thread markers`, plus a before/after split at a timestamp.
- **Example from:** [browser/browser/components/places/tests/browser/browser_library_bookmark_pages.js.files/profiler-cli-feedback.md](reports/browser/browser/components/places/tests/browser/browser_library_bookmark_pages.js.files/profiler-cli-feedback.md)

## 99. Search with an unknown field name silently matches nothing (2)

`thread markers --search "Type:PBrowser::Msg_UpdateDimensions"` uses the field label printed by `marker info`, but search needs raw payload keys (never shown in text output), so it silently matches nothing.

- **Question:** Which IPC markers have Type PBrowser::Msg_UpdateDimensions?
- **Command:** `profiler-cli thread markers --thread t-0 --search "Type:PBrowser::Msg_UpdateDimensions" --list --limit 0`
- **Expected:** The IPC markers whose `Type` field is `PBrowser::Msg_UpdateDimensions`.
- **Got:** `No markers match the specified filters.`
- **Suggestion:** Accept printed labels as key aliases, or warn when a `field:` prefix names no key on the thread.
- **Example from:** [mac/browser/components/extensions/test/browser/browser_ext_menus_targetElement_extension.js.files/profiler-cli-feedback.md](reports/mac/browser/components/extensions/test/browser/browser_ext_menus_targetElement_extension.js.files/profiler-cli-feedback.md)

## 100. Group-by and `profile markers` truncate results without saying so (2)

`profile markers --search` printed header `40 markers across 2 of 17 threads` and exactly 40 rows, cutting off the last 4 TEST-UNEXPECTED-FAIL markers with no "N more" line; the header count reads as the total.

- **Question:** What are all of this test's log markers, including its failures?
- **Command:** `profiler-cli profile markers --search test_select_input_change_event --session test_select_input_change_event.html-1`
- **Expected:** Every matching marker, or a `… N more (--limit 0 for all)` line as thread markers prints.
- **Got:** `40 markers across 2 of 17 threads`, 40 rows ending mid-test; the 4 last TEST-UNEXPECTED-FAIL (m-42..m-45) missing.
- **Suggestion:** Print a truncation line and make the header give total vs shown.
- **Example from:** [mac/dom/html/test/forms/test_select_input_change_event.html.files/profiler-cli-feedback.md](reports/mac/dom/html/test/forms/test_select_input_change_event.html.files/profiler-cli-feedback.md)

## 101. No numeric comparison, sort or top-N on payload fields (2)

`thread markers --search` can't filter or sort on a numeric payload value, and `--list` rows show no payload. Finding NetIO markers with large transfers needed `--json` and a Python filter `recv_bytes > 50000`.

- **Question:** Which NetIO samples in this range carry large (loopback) transfers?
- **Command:** `profiler-cli thread markers --search NetIO --list --limit 60 --session 1809667.talos-2`
- **Expected:** Each NetIO marker with its Received/Sent bytes, or a filter on a payload value.
- **Got:** Name, time and duration only (`m-3  NetIO  t=4m23s  167ms`), 45 identical-looking rows.
- **Suggestion:** Show table-label fields in `--list`, or support numeric filters like `--search recv_bytes>50000`.
- **Example from:** [intermittent/bugs/1809667.talos.files/profiler-cli-feedback.md](reports/intermittent/bugs/1809667.talos.files/profiler-cli-feedback.md)

## 102. `search=` from a loaded URL silently filters sample queries (2)

Loading a calltree URL with `search=testGetValue` silently cut every `thread samples` query to matching samples, while `status` said "Filters: none" and `filter clear`/`zoom clear` changed nothing.

- **Question:** What does the whole thread's sample profile look like?
- **Command:** `profiler-cli thread samples --include-idle`
- **Expected:** The whole thread, or the URL's search shown as an active filter that `filter clear` removes.
- **Got:** "4745 running samples", every function at 100%; `status` said "Filters: none". Reloading without `search=` gave 5079 samples.
- **Suggestion:** Show the URL's search as an active filter in load output and `status`, and let `filter clear` remove it.
- **Example from:** [mac/toolkit/components/telemetry/tests/unit/test_TelemetryEnvironment_search.js.files/profiler-cli-feedback.md](reports/mac/toolkit/components/telemetry/tests/unit/test_TelemetryEnvironment_search.js.files/profiler-cli-feedback.md)

## 103. `counter info` shows 0% for buckets with no counter sample (2)

`counter info` on Process CPU prints 0% for a bucket with no counter sample; samples exist at 4.3788s and 4.4024s only. A different zoom shows 98%/27% for the same data. A reviewed report quoted the 0% as "idle".

- **Question:** What did the parent process CPU counter measure over this interval?
- **Command:** `profiler-cli counter info c-1 --session review-test_ext_menu_startup.js-1`
- **Expected:** Buckets with no sample shown as "no sample" (or the covering sample's value), with sample times listed.
- **Got:** `[4.380s - 4.400s] 0%`, though the sample closing that interval is at 27% of the view's peak.
- **Suggestion:** Print `-`/"no sample" for empty buckets, or align buckets to sample intervals.
- **Example from:** [mac/browser/components/extensions/test/xpcshell/test_ext_menu_startup.js.files/profiler-cli-feedback.md](reports/mac/browser/components/extensions/test/xpcshell/test_ext_menu_startup.js.files/profiler-cli-feedback.md)

## 104. `thread info` hides low CPU activity in narrow zooms (1)

After `zoom push 57.495,57.530`, `thread info` text says "No significant activity", yet `thread info --json` on a similar zoom gives cpuActivity of 16.75 ms over 38 ms, the key fact. The text hides below-threshold CPU.

- **Question:** How much CPU did the main thread get between two samples, and when was each sample taken?
- **Command:** `profiler-cli thread info`
- **Expected:** The thread's CPU in that window.
- **Got:** "No significant activity." while `--json` `cpuActivity` shows 16.75 ms in 57.491s-57.529s.
- **Suggestion:** Print the actual CPU time in narrow zooms instead of hiding it; a per-sample list (time, CPU delta, leaf) for a range.
- **Example from:** [devtools/devtools/client/debugger/test/mochitest/browser_dbg-keyboard-navigation.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/debugger/test/mochitest/browser_dbg-keyboard-navigation.js.files/profiler-cli-feedback.md)

## 105. No option to show neighbouring markers around matches (1)

`thread markers --list` can't show neighbouring markers around matches, and doesn't show markerIndex. Untagged harness INFO lines (1,149 at the same ms) couldn't be attributed to a test without a script printing neighbours.

- **Question:** Which `not killing -- proc or pid unknown` / `xpcshell return code` line belongs to this test?
- **Command:** `profiler-cli thread markers --search "not killing,xpcshell return code,creationTime_01" --list --limit 0 --json`
- **Expected:** A way to see markers before/after each match in marker-index order.
- **Got:** Flat list; adjacency only recoverable via a script and `marker info --json` for `markerIndex`.
- **Suggestion:** Add `--context N` to `--list`, or show `markerIndex` in the text list.
- **Example from:** [devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_creationTime_01.js.files/profiler-cli-feedback.md](reports/devtools/devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_creationTime_01.js.files/profiler-cli-feedback.md)

## 106. Aggregate view can't split Runnable markers by runnable name (1)

The `thread markers --search "AsyncExecuteStatements"` aggregate showed one group `Runnable 50 markers`; the runnable name is only in the label, so counting notifyResults vs notifyComplete runnables needed --list --json and a Python Counter. The output suggested no --group-by key.

- **Question:** How many AsyncExecuteStatements::notifyResultsOnCallingThread vs notifyCompleteOnCallingThread runnables ran in a range?
- **Command:** `profiler-cli thread markers --session review-test_async_transactions.js-1 --search "AsyncExecuteStatements" --limit 0`
- **Expected:** The aggregate split by runnable name.
- **Got:** `Runnable 50 markers`, one group.
- **Suggestion:** Group Runnable markers by runnable name by default, or hint the --group-by key that does.
- **Example from:** [intermittent/toolkit/components/places/tests/unit/test_async_transactions.js.files/profiler-cli-feedback.md](reports/intermittent/toolkit/components/places/tests/unit/test_async_transactions.js.files/profiler-cli-feedback.md)

## 107. `counter info` values contradict between zoomed and unzoomed views (1)

`counter info c-2` (Process CPU of a Utility process with 8 ms CPU total) shows 0.2% per bucket unzoomed but 47-68% per bucket after `zoom push 5.079,5.405` over the same span.

- **Question:** Was the Utility process busy during this window?
- **Command:** `profiler-cli counter info c-2`
- **Expected:** About 0%, as the unzoomed buckets (0.2%) show.
- **Got:** Every bucket in the zoomed range between 47% and 68%.
- **Example from:** [browser/browser/components/aboutlogins/tests/browser/browser_createLogin.js.files/profiler-cli-feedback.md](reports/browser/browser/components/aboutlogins/tests/browser/browser_createLogin.js.files/profiler-cli-feedback.md)

## 108. No filter to drop nested same-name markers (1)

Listing a recursive marker (`DocAccessible::PruneOrInsertSubtree`) shows 58+ nested copies with near-identical durations, burying the 4 top-level calls; no option drops markers nested in a same-name marker.

- **Question:** How many top-level calls of this recursive marker were there, and how long did each take?
- **Command:** `thread markers --search name:DocAccessible::PruneOrInsertSubtree --min-duration 1 --list`
- **Expected:** Only the top-level calls with their durations.
- **Got:** 58+ markers with near-identical durations (nested recursion frames).
- **Suggestion:** Add a `--top-level`/`--no-nested` filter keeping markers not contained in another of the same name.
- **Example from:** [browser/browser/components/preferences/tests/home/browser_homepage_firefox_home_support_firefox.js.files/profiler-cli-feedback.md](reports/browser/browser/components/preferences/tests/home/browser_homepage_firefox_home_support_firefox.js.files/profiler-cli-feedback.md)

## 109. No output-size guard on huge `--list --limit 0` results (1)

`--search` with `name:task` substring-matched every `TaskController::AddTask`; `--list --limit 0` then dumped 82,196 rows (9.9 MB) into the agent's context without warning.

- **Question:** Where are the dozen `setTimeout`/`task` markers of this test?
- **Command:** `profiler-cli thread markers --session <s> --search "setTimeout() for,...,name:task,..." --list --limit 0`
- **Expected:** The dozen intended markers, or a warning that the filter matched far more than a list can show.
- **Got:** 82,196 rows, 9.9 MB of output.
- **Suggestion:** Above a few thousand rows, print the count and top matching names; require an explicit flag to print all.
- **Example from:** [browser/browser/components/profiles/tests/unit/test_usageProfileGroupID.js.files/profiler-cli-feedback.md](reports/browser/browser/components/profiles/tests/unit/test_usageProfileGroupID.js.files/profiler-cli-feedback.md)

## 110. `profile meta` shows version as product name for xpcshell (1)

`profile meta` on an xpcshell profile (empty product name) prints `Name:  158`, reading as a product named "158" instead of the version.

- **Question:** What product and version produced this profile?
- **Command:** `profiler-cli profile meta --session 2064983-1`
- **Expected:** A line reading as the version, e.g. `Version: 158`.
- **Got:** `Name:  158` (browser-chrome profiles show `Name: Firefox 158 (build 20260910163201)`).
- **Suggestion:** Print `Version: 158` when the product name is empty.
- **Example from:** [intermittent/bugs/2064983.files/profiler-cli-feedback.md](reports/intermittent/bugs/2064983.files/profiler-cli-feedback.md)

## 111. `thread network` counts START-only requests as in flight (1)

In a zoomed range, `thread network` counts network markers with only STATUS_START (no end) as in flight for the whole window: 48 of 50 rows were requests from an earlier test, inflating concurrency.

- **Question:** Which requests were active in the zoomed 1.4 s window?
- **Command:** `profiler-cli thread network --sort start --limit 0`
- **Expected:** Only requests active in the zoomed 224.6-226 s window.
- **Got:** 48 of 50 rows were `sjs_content-type-test-server.sjs?sts=304` START-only markers from t=2m31s, "in flight 100%, peak 49 concurrent".
- **Suggestion:** Flag START-only markers as "no end marker recorded" rather than in flight, or offer an option to hide them.
- **Example from:** [devtools/devtools/client/netmonitor/test/browser_net_view-source-requests.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/netmonitor/test/browser_net_view-source-requests.js.files/profiler-cli-feedback.md)

## 112. Marker duration stats lack median and percentiles (1)

The aggregate `thread markers --search DispatchTransaction` gives only `interval: min/avg/max`, so comparing duration distributions between two profiles (offset vs proportional scale-up) needed --json and Python for median and percentiles. Same for `thread network` phases (totals only).

- **Question:** How are this interval marker's durations distributed, and how did that shift between two profiles?
- **Command:** `profiler-cli thread markers --search DispatchTransaction --session <s>`
- **Expected:** Median and a few percentiles next to min/avg/max.
- **Got:** `interval: min=73.981ms, avg=129.54ms, max=208.41ms` only.
- **Suggestion:** Add p50/p90 to the aggregate line, and per-phase medians in thread network.
- **Example from:** [intermittent/browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_merino.js.files/profiler-cli-feedback.md](reports/intermittent/browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_merino.js.files/profiler-cli-feedback.md)

## 113. Space-separated handle list in one argument rejected (1)

In zsh, passing a list of handles to `marker info` as one unquoted variable yields a single argument and fails with "Invalid marker handle m-1 m-2 ...", breaking scripts that tabulate CC markers.

- **Question:** What did the CC free, step by step?
- **Command:** `profiler-cli marker info <handles...> --json`
- **Expected:** The markers for all handles in the space-separated list.
- **Got:** "Invalid marker handle m-1 m-2 ..."
- **Suggestion:** Accept a space-separated handle list in a single argument.
- **Example from:** [mac/dom/indexedDB/test/test_event_listener_leaks.html.files/profiler-cli-feedback.md](reports/mac/dom/indexedDB/test/test_event_listener_leaks.html.files/profiler-cli-feedback.md)

## 114. A loaded link's range persists as a hidden zoom level (1)

After loading a link with `range=9553000u5000` and pushing/popping its own zoom, `thread markers --search "three source actors"` still used the link's 5 ms range, missing the TEST-UNEXPECTED-FAIL 5 s later; only the header hints at it.

- **Question:** Where is the "three source actors" failure relative to the waitForCondition marker?
- **Command:** `profiler-cli thread markers --session S --thread t-0 --search "three source actors" --list`
- **Expected:** After popping own zoom, results over the full profile.
- **Got:** Only the `waitForCondition` marker, not the `TEST-UNEXPECTED-FAIL` 5 s later; link's 5 ms range still applied.
- **Suggestion:** Print "(view restricted to the loaded link's range)" next to the match count.
- **Example from:** [devtools/devtools/client/debugger/test/mochitest/browser_dbg-sources-with-many-actors.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/debugger/test/mochitest/browser_dbg-sources-with-many-actors.js.files/profiler-cli-feedback.md)

## 115. Same field formatted differently in `marker info` and `--json` (1)

The iowait of a `CPU Use` marker prints as `iowait: 0.875` in `marker info` text but as a `%`-formatted string (`88.3%`/`87.5%`-style) in `--json` `iowait_pct`, so the two views don't obviously match.

- **Question:** Was the machine I/O-bound during each of these 9 time windows?
- **Command:** `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0`
- **Expected:** The iowait / CPU % per row, in a consistent format across views.
- **Got:** Rows with name, time, duration only; `marker info` shows `iowait: 0.875`, `--json` a `%` string.
- **Suggestion:** Format fields identically in text and JSON; show values (or min/mean/max) in the list.
- **Example from:** [intermittent/browser/components/backup/tests/marionette/test_backup_replace_current_profile.py.files/profiler-cli-feedback.md](reports/intermittent/browser/components/backup/tests/marionette/test_backup_replace_current_profile.py.files/profiler-cli-feedback.md)

## 116. `load` hangs fetching a profile, with no timeout or retry (1)

`load` of a from-url profiler link stalled twice in the daemon at "Fetching profile from https://firefox-ci-tc..." until the client timeout, while curl fetched the same 110 MB artifact in 5 s; no fetch error, progress or retry. A third load worked.

- **Question:** Load this profile to review the report.
- **Command:** `profiler-cli load "<url>" --session S`
- **Expected:** The profile loaded, or a fetch error.
- **Got:** Daemon log stops at "Fetching profile from https://firefox-ci-tc..." until "Profile load timeout after 280000ms".
- **Suggestion:** Add a fetch timeout, progress reporting and retry in the daemon.
- **Example from:** [intermittent/browser/components/tabbrowser/test/browser/tabs/browser_tab_groups.js.files/profiler-cli-feedback.md](reports/intermittent/browser/components/tabbrowser/test/browser/tabs/browser_tab_groups.js.files/profiler-cli-feedback.md)

## 117. List rows repeat a redundant Test Name suffix (1)

When searching by test name, every `--list` row ends with the redundant ` — dom/media/test/test_eme_waitingforkey.html` Test Name field, and rows cut at terminal width lose the end of long INFO messages.

- **Question:** What does the test's own log say, readably?
- **Command:** `profiler-cli thread markers --session S --search test_eme_waitingforkey.html --list --limit 0`
- **Expected:** Readable log lines with full messages.
- **Got:** Every row ends with ` — dom/media/test/test_eme_waitingforkey.html`; long INFO messages lose their end.
- **Suggestion:** Drop the Test Name suffix when the search already selects that test.
- **Example from:** [intermittent/dom/media/test/test_eme_waitingforkey.html.files/profiler-cli-feedback.md](reports/intermittent/dom/media/test/test_eme_waitingforkey.html.files/profiler-cli-feedback.md)

## 118. `profile meta` lacks CPU core count (1)

`profile meta` (text and `--json`) on a per-test xpcshell profile shows no CPU name or core count, so Process CPU percentages can't be related to machine capacity.

- **Question:** How many cores does this machine have?
- **Command:** `profiler-cli profile meta`
- **Expected:** CPU name and core count.
- **Got:** No CPU name or core count in text or JSON.
- **Example from:** [mac/toolkit/components/places/tests/sync/test_bookmark_chunking.js.files/profiler-cli-feedback.md](reports/mac/toolkit/components/places/tests/sync/test_bookmark_chunking.js.files/profiler-cli-feedback.md)

## 119. `screenshots` aborts on one frame that isn't a data URL (1)

`screenshots --at 1030.07` wrote 2 of 3 windows then aborted on m-8773 (`url: "825038fca2c567f30"`, apparently an unresolved string index); the failing frame was the window under investigation. `--range` and `--json` abort the same way.

- **Question:** What did the AI window look like just before the Enter?
- **Command:** `profiler-cli screenshots --session <s> --at 1030.07 -o <dir>`
- **Expected:** One image per window at that time.
- **Got:** `Error: Screenshot for m-8773 is not a base64 data URL, so it cannot be written to a file.` after writing 2 of 3.
- **Suggestion:** Skip the bad frame with a warning and fall back to that window's previous good frame.
- **Example from:** [browser/browser/components/aiwindow/ui/test/browser/browser_smartwindow_smartbar_model_select.js.files/profiler-cli-feedback.md](reports/browser/browser/components/aiwindow/ui/test/browser/browser_smartwindow_smartbar_model_select.js.files/profiler-cli-feedback.md)

## 120. `profile info --search` with a space matches nothing (1)

`profile info --search "Parent Process"` returns an empty list, while `--search Parent` works.

- **Question:** Which threads belong to the parent process?
- **Command:** `profiler-cli profile info --session <s> --search "Parent Process"`
- **Expected:** The parent process and its threads.
- **Got:** `Processes and threads matching 'Parent Process':` and an empty list.
- **Suggestion:** Match search terms containing spaces.
- **Example from:** [mac/browser/components/extensions/test/browser/browser_ext_persistent_storage_permission_indication.js.files/profiler-cli-feedback.md](reports/mac/browser/components/extensions/test/browser/browser_ext_persistent_storage_permission_indication.js.files/profiler-cli-feedback.md)

## 121. Can't search for markers with an empty field value (1)

DOMEvent markers from internal events (e.g. `eMouseEnterIntoWidget`) have an empty `eventType`, but `thread markers --search "eventType:"` matches nothing, so they can only be found by listing every DOMEvent in a narrow zoom.

- **Question:** Which DOMEvents have an empty event type?
- **Command:** `thread markers --search "eventType:"`
- **Expected:** The DOMEvent markers whose eventType is empty.
- **Got:** Matches nothing.
- **Example from:** [mac/widget/tests/test_bug596600.xhtml.files/profiler-cli-feedback.md](reports/mac/widget/tests/test_bug596600.xhtml.files/profiler-cli-feedback.md)

## 122. `screenshots` has no list-only mode (1)

`screenshots` always writes images; `-o /dev/null` to just get the listing fails, so listing screenshot times requires a scratch directory.

- **Question:** What are the screenshot timestamps in this range?
- **Command:** `profiler-cli screenshots --range 2.9,9.3 -o /dev/null --session ...`
- **Expected:** The list of screenshot timestamps.
- **Got:** `Error: EEXIST: file already exists, mkdir '/dev/null'`.
- **Suggestion:** Add a list-only mode (e.g. `--list`) that writes no files.
- **Example from:** [browser/browser/components/genai/tests/browser/browser_chat_page.js.files/profiler-cli-feedback.md](reports/browser/browser/components/genai/tests/browser/browser_chat_page.js.files/profiler-cli-feedback.md)

## 123. Time blocked in a syscall collapsed into one sample without note (1)

Zoomed on a 270 ms runnable blocked in `libc.so.6!_unlink`, `thread samples` shows `total: 1`: idle-deduplicated samples collapse the block into one sample, with no note that the thread was blocked or for how long.

- **Question:** What was the main thread doing during this 270 ms runnable?
- **Command:** `profiler-cli thread samples --limit 3 --session <s>`
- **Expected:** About 27 samples in `nsIFile.remove` → `_unlink`, or a sign the thread was blocked in a syscall.
- **Got:** `total: 1`; even `thread functions --include-idle --search _unlink` over 25–30 s counted only 6 samples.
- **Suggestion:** Report blocking in the leaf frame, e.g. "blocked in _unlink for 267 ms (1 distinct sample)".
- **Example from:** [browser/browser/components/extensions/test/browser/browser_ext_tabs_executeScript_good.js.files/profiler-cli-feedback.md](reports/browser/browser/components/extensions/test/browser/browser_ext_tabs_executeScript_good.js.files/profiler-cli-feedback.md)

## 124. No per-library rollup of sample self time (1)

`thread samples` / `thread functions` give no per-library self-time rollup; getting ntdll's unwinder share (41%) meant summing 10 functions by hand.

- **Question:** What share of samples is in one native library (ntdll's unwinder)?
- **Command:** `profiler-cli thread functions --search "ntdll.dll!Rtl"`
- **Expected:** A per-library share of self time.
- **Got:** Every native frame has total == self; summed 15.6 + 15.2 + 4.9 + 4.4 + ... by hand to get 41%.
- **Suggestion:** A per-library rollup of self time (ntdll.dll, clang_rt.asan_dynamic, xul.dll) in `thread samples`.
- **Example from:** [browser/browser/components/aiwindow/ui/test/browser/browser_aiwindow_smartbar_telemetry.js.files/profiler-cli-feedback.md](reports/browser/browser/components/aiwindow/ui/test/browser/browser_aiwindow_smartbar_telemetry.js.files/profiler-cli-feedback.md)

## 125. Call tree truncates function names instead of long path prefixes (1)

`thread samples` truncates frames at the end, so the long CI path prefix fills the width and the function name is cut (`...test_dynamicLauncher.js!expect...`), requiring `function expand` per frame.

- **Question:** Which test functions are in the stack of this long task?
- **Command:** `profiler-cli thread samples --session browser-test_dynamicLauncher.js-4`
- **Expected:** Function names like `test_dynamicLauncher.js!expectDbusMockCall`.
- **Got:** `/builds/worker/workspace/build/tests/xpcshell/tests/browser/components/shell/test/unit/test_dynamicLauncher.js!expect...` and `...!test_i...`
- **Suggestion:** Truncate the path in the middle rather than the function name.
- **Example from:** [browser/browser/components/shell/test/unit/test_dynamicLauncher.js.files/profiler-cli-feedback.md](reports/browser/browser/components/shell/test/unit/test_dynamicLauncher.js.files/profiler-cli-feedback.md)

## 126. No samples-during-each-marker view (1)

`thread markers --min-duration 50 --list` found 9 long MessageEventToParentRunnable tasks, but finding what each ran needed `zoom push m-N` + `thread samples --json` + a script, per marker.

- **Question:** Which function dominates each of these N long markers?
- **Command:** `profiler-cli thread markers --search name:MessageEventToParentRunnable --min-duration 50 --list`
- **Expected:** Top self/total function per listed marker (e.g. `--with-top-function`), or `thread samples --during-marker m-N`.
- **Got:** 9 long tasks listed with no sample info; a zoom/samples/zoom-pop loop per marker was required.
- **Suggestion:** Add `--with-top-function` to marker lists, or let `thread samples` take a marker handle as range.
- **Example from:** [browser/browser/components/shell/test/unit/test_dynamicLauncher.js.files/profiler-cli-feedback.md](reports/browser/browser/components/shell/test/unit/test_dynamicLauncher.js.files/profiler-cli-feedback.md)

## 127. field:value search matches markers lacking that field (1)

`thread markers --search 'test:<path>'` returns markers lacking a `test` field (DocShell, DOMWindow about:blank), so the field search doesn't narrow to the single `test` marker.

- **Question:** Which is the `test` marker of this one test (to check its markerIndex)?
- **Command:** `profiler-cli thread markers --session review-browser_net_statistics-01.js-2 --search 'test:devtools/client/netmonitor/test/browser_net_statistics-01.js' --list`
- **Expected:** The single `test` marker whose `test` field is that path, maybe plus its FAIL/PASS instants.
- **Got:** 44 markers, led by `DocShell` and `DOMWindow` about:blank markers with no `test` field; the `test` marker wasn't on the first screen.
- **Suggestion:** Make `field:value` match only markers that have that field.
- **Example from:** [devtools/devtools/client/netmonitor/test/browser_net_statistics-01.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/netmonitor/test/browser_net_statistics-01.js.files/profiler-cli-feedback.md)

## 128. No option to print a profiler link per listed marker (1)

`thread markers --list` cannot print a profiler URL per row, so linking every marker matching a search needs `--json | jq` for handles, then one `profile-link.py --marker` call per handle.

- **Question:** A profile link for each marker matching `--search test_frameactor-02` in this session.
- **Command:** `profiler-cli thread markers --session S --search test_frameactor-02 --list --limit 0 --json | jq -r '.flatMarkers[].handle'`
- **Expected:** A profiler URL next to each listed handle.
- **Got:** Only handles; each needs a separate profile-link.py --marker call.
- **Suggestion:** Add a --links option to thread markers --list, or profile-link.py --search.
- **Example from:** [devtools/devtools/server/tests/xpcshell/test_frameactor-02.js.files/profiler-cli-feedback.md](reports/devtools/devtools/server/tests/xpcshell/test_frameactor-02.js.files/profiler-cli-feedback.md)

## 129. `marker stack` starts with the profiler's own capture frames (1)

`marker stack m-96` on a `NotifyObservers` TextStack marker starts with two `XUL!mozilla::base_profiler_markers_detail::AddMarkerToBuffer<...TextStackMarker...>` frames, each several hundred characters, before the first useful frame; same for every NotifyObservers and importESModule marker.

- **Question:** Which frames led to this NotifyObservers marker?
- **Command:** `profiler-cli marker stack m-96 --session review-bct-1`
- **Expected:** The stack starting at the code that sent the notification.
- **Got:** Frames [1] and [2] are long `AddMarkerToBuffer<...TextStackMarker...>` template instantiations.
- **Suggestion:** Drop the profiler's own marker-capture frames from `marker stack` output, or shorten them to one line.
- **Example from:** [mac/browser/components/places/tests/browser/browser_bookmarks_change_title.js.files/profiler-cli-feedback.md](reports/mac/browser/components/places/tests/browser/browser_bookmarks_change_title.js.files/profiler-cli-feedback.md)

## 130. Same zoom gives different sample counts depending on load source (1)

After the same `zoom push 41.9547,42.1127`, `thread samples --include-idle` reports 3 samples in a session loaded from a profiler link and 4 in one loaded from the raw Taskcluster URL.

- **Question:** How many samples does t-0 have in a 158 ms window?
- **Command:** `profiler-cli thread samples --include-idle`
- **Expected:** The same count in both sessions, since both print the same view (158.00ms).
- **Got:** `3 running samples` in the link-loaded session, `4 running samples` in the raw-URL one.
- **Suggestion:** Same count regardless of load source; show sample count in `thread info` under a zoom.
- **Example from:** [mac/devtools/client/storage/test/browser_storage_delete_usercontextid.js.files/profiler-cli-feedback.md](reports/mac/devtools/client/storage/test/browser_storage_delete_usercontextid.js.files/profiler-cli-feedback.md)

## 131. `thread samples` shows no samples while `thread info` counts many (1)

`thread samples --include-idle` over the full range reports 0 samples on a thread that `thread info` says has 130 samples, with no reason given (e.g. missing stacks).

- **Question:** What were the samples on this thread, and what were the gaps between them?
- **Command:** `profiler-cli thread samples --include-idle --session review-browser_103_cleanup.js-1`
- **Expected:** The 130 samples `thread info` counts, or a reason they cannot be shown.
- **Got:** `Categories (0 running samples)` and `No samples in the current view.`
- **Suggestion:** Show the samples, or explain why they are excluded (for example, no stacks).
- **Example from:** [mac/netwerk/test/browser/browser_103_cleanup.js.files/profiler-cli-feedback.md](reports/mac/netwerk/test/browser/browser_103_cleanup.js.files/profiler-cli-feedback.md)

## 132. Can't extract data: images from Image Load/Paint markers (1)

To see a `drawWindow` screenshot, the agent regex-extracted the `data:image/png;base64` URL from an `Image Load` marker via `marker info --json` and hand-wrote a PNG decoder; no command saves such images.

- **Question:** What image did the test's drawWindow screenshot contain?
- **Command:** `profiler-cli marker info m-62 --session <s> --json`
- **Expected:** A command writing the marker's data: image to a file.
- **Got:** Only the base64 URL inside the JSON payload.
- **Suggestion:** Let `marker screenshot` accept Image Load/Paint markers with data: URLs, as it does for CompositorScreenshot.
- **Example from:** [mac/browser/base/content/test/browser-favicons/browser_favicon_svg.js.files/profiler-cli-feedback.md](reports/mac/browser/base/content/test/browser-favicons/browser_favicon_svg.js.files/profiler-cli-feedback.md)

## 133. `-category:X` search term doesn't exclude the category (1)

`thread markers --search -category:Accessibility,...` still lists Accessibility markers; only excluding each marker name with `-name:` worked.

- **Question:** Which markers other than the accessibility flood happened in this range?
- **Command:** `profiler-cli thread markers --search -category:Accessibility,-name:Preference\ Read --list`
- **Expected:** `-category:` to exclude the category, as `--category` includes one.
- **Got:** Accessibility markers still listed.
- **Example from:** [browser/browser/components/preferences/tests/home/browser_homepage_firefox_home.js.files/profiler-cli-feedback.md](reports/browser/browser/components/preferences/tests/home/browser_homepage_firefox_home.js.files/profiler-cli-feedback.md)

## 134. No filter of samples by per-sample CPU usage (1)

`thread samples` has no filter on per-sample CPU delta, so comparing leaf frames of zero-CPU samples vs CPU-consuming ones (to tell preempted from blocked) required downloading the profile JSON and scripting.

- **Question:** When the thread got no CPU, was it preempted or blocked?
- **Command:** `none available (thread samples has no per-sample CPU filter)`
- **Expected:** Something like `thread samples --cpu-below 1%` / `--cpu-above` to compare leaf frames.
- **Got:** Nothing to filter on.
- **Suggestion:** Add `--cpu-below`/`--cpu-above` filters on per-sample CPU delta to `thread samples*` commands.
- **Example from:** [mac/toolkit/components/search/tests/xpcshell/searchconfigs/test_searchconfig.js.files/profiler-cli-feedback.md](reports/mac/toolkit/components/search/tests/xpcshell/searchconfigs/test_searchconfig.js.files/profiler-cli-feedback.md)

## 135. Nothing helps locate where an uncaught promise rejection came from (1)

For an uncaught-rejection failure, `marker stack` on the failure marker gives only the harness reporting stack (PromiseTestUtils.assertNoUncaughtRejections), 150 ms late. The origin (a MozPromise ResolveOrRejectRunnable just before dom::FlushRejections) took hand-filtering every marker in the prior 200 ms.

- **Question:** Where was this "uncaught rejection" (NS_ERROR_NOT_AVAILABLE) created?
- **Command:** `profiler-cli marker stack m-16 --session browser-browser_identityBlock_focus.js-1`
- **Expected:** Something pointing at the code that rejected the promise.
- **Got:** The harness's reporting stack (`PromiseTestUtils.assertNoUncaughtRejections` / `nextTest`), 150 ms after the fact.
- **Suggestion:** For uncaught-rejection failures or NotifyUnhandledRejections runnables, name the runnable that ran just before the corresponding dom::FlushRejections.
- **Example from:** [browser/browser/base/content/test/browser-siteIdentity/browser_identityBlock_focus.js.files/profiler-cli-feedback.md](reports/browser/browser/base/content/test/browser-siteIdentity/browser_identityBlock_focus.js.files/profiler-cli-feedback.md)

## 136. No way to show the last N rows of a marker list (1)

`thread markers --list` shows only the head of a filtered list; there's no `--last N`/`--reverse`, so seeing the last TEST-PASS means printing all 1,400 rows or using `--json`.

- **Question:** What is the last marker of this filtered list (how far did the test get before being killed)?
- **Command:** `profiler-cli thread markers --search "name:TEST-PASS" --list --limit 0`
- **Expected:** A way to see the tail, e.g. `--last N` or `--reverse`.
- **Got:** Only the head by default; `--limit 0` prints all 1,400 rows.
- **Suggestion:** Add `--last N` or `--reverse` to `thread markers --list`.
- **Example from:** [browser/browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_yelp.js.files/profiler-cli-feedback.md](reports/browser/browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_yelp.js.files/profiler-cli-feedback.md)

## 137. Markers/sec rate computed over first-to-last span, inflating bursts (1)

In an 88 s zoom, `thread markers` Frequency Analysis reported `IPCIn: 31183.8 markers/sec` for 14 markers, apparently computing the rate over a few-hundred-µs burst between first and last marker. A nearly idle Compositor thread reads as busy.

- **Question:** Was the Compositor thread active while the test waited?
- **Command:** `profiler-cli zoom push 406.5,494.7 --session X; profiler-cli thread markers --thread t-3 --session X`
- **Expected:** A rate over the view: 14 IPCIn markers in 88 s, about 0.16/s.
- **Got:** `IPCIn: 31183.8 markers/sec (interval: min=23.001μs, avg=34.535μs, ...)`.
- **Suggestion:** Compute markers/sec over the view duration, or label the span it uses.
- **Example from:** [devtools/devtools/client/debugger/test/mochitest/browser_dbg-pause-on-next.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/debugger/test/mochitest/browser_dbg-pause-on-next.js.files/profiler-cli-feedback.md)

## 138. `function annotate` line info contradicts its own header (1)

`function annotate f-2980` (zoomed to 7.575,7.583) says `0 of 1 samples have line number information` yet puts that sample on line 411 (`--json`: `samplesWithLineInfo: 0`, line 411 `totalSamples: 1`), so it is unclear whether line 411 is real.

- **Question:** Which source line of this function was running in the sample?
- **Command:** `profiler-cli function annotate f-2980 --session bcs-finalized-1`
- **Expected:** Either the sample is attributed to a line, or it is not.
- **Got:** Header `0 of 1 samples have line number information`, while the table puts that sample on line 411.
- **Example from:** [intermittent/browser/components/aiwindow/models/tests/browser/browser_conversation_starters.js.already-finalized.files/profiler-cli-feedback.md](reports/intermittent/browser/components/aiwindow/models/tests/browser/browser_conversation_starters.js.already-finalized.files/profiler-cli-feedback.md)

## 139. DOM Worker threads not identified by script URL (1)

`thread list` names all 35 parent-process worker threads just `DOM Worker`, with no script URL, and `profile info --search` can't find them, so locating the source-map-loader worker took 36 `thread select` + `thread samples` calls.

- **Question:** Which DOM Worker thread runs resource://devtools/client/shared/source-map-loader/worker.js?
- **Command:** `profiler-cli thread list --session …`
- **Expected:** A way to find the thread running that worker script.
- **Got:** Every row named `DOM Worker`, no script URL; `profile info --search source-map` matches nothing.
- **Suggestion:** Show the worker script URL (from the `WorkerThreadPrimaryRunnable::Run` label frame) in `thread list`/`profile info`, searchable.
- **Example from:** [mac/devtools/client/framework/test/browser_toolbox_backward_forward_navigation.js.files/profiler-cli-feedback.md](reports/mac/devtools/client/framework/test/browser_toolbox_backward_forward_navigation.js.files/profiler-cli-feedback.md)

## 140. `marker info --json` truncates stacks at 20 frames (1)

`marker info --json` over 247 stack-bearing markers cut 49 stacks at 20 frames (`"truncated": true`) with no option for more; the searched `requestIdleCallback handler` frame was at depth 22.

- **Question:** Does any marker in this range have `setCanRender` in its stack?
- **Command:** `profiler-cli marker info m-a m-b ... --json`
- **Expected:** Each marker's full stack.
- **Got:** 49 of 247 stacks with `"truncated": true` at 20 frames; no option to get more.
- **Suggestion:** Add a `--full-stacks` flag, or a frame search on `thread markers`.
- **Example from:** [mac/devtools/client/framework/test/browser_toolbox_keyboard_navigation_notification_box.js.files/profiler-cli-feedback.md](reports/mac/devtools/client/framework/test/browser_toolbox_keyboard_navigation_notification_box.js.files/profiler-cli-feedback.md)

## 141. profile-link.py can't express field:value searches in links (1)

A field search that answers the question exactly in the CLI has no URL equivalent. `profile-link.py --search focus` opens 60 unrelated markers (mostly `Preference Read` of `accessibility.tabfocus`) and gives no warning.

- **Question:** Did any focus, activate or deactivate DOM event reach the parent main thread?
- **Command:** `profiler-cli thread markers --search "eventType:focus,eventType:activate,eventType:deactivate" --list --limit 0`
- **Expected:** A link showing the same result, or a warning that the field search has no URL form.
- **Got:** CLI matched only `mochitest-load`; the link showed 60 unrelated markers, so the whole 1006-marker DOMEvent list was linked instead.
- **Suggestion:** Have profile-link.py say when a field search has no URL equivalent and suggest the closest bare search.
- **Example from:** [intermittent/bugs/2022292.files/profiler-cli-feedback.md](reports/intermittent/bugs/2022292.files/profiler-cli-feedback.md)

## 142. `profile-link.py --check` prints only the URL (1)

`profile-link.py --check` prints only the URL, identical to the output without --check, so the agent cannot tell whether the check ran, which thread/marker the link opens on, or whether it passed.

- **Question:** Does the generated link open on the intended marker?
- **Command:** `python3 profile-link.py --session browser_text_input.js-3 --marker m-807 --check`
- **Expected:** A line saying which thread and marker the link opens on, per --help.
- **Got:** The URL alone, same as without --check.
- **Suggestion:** Print the thread/marker the link resolves to and a pass/fail result.
- **Example from:** [mac/accessible/tests/browser/mac/browser_text_input.js.files/profiler-cli-feedback.md](reports/mac/accessible/tests/browser/mac/browser_text_input.js.files/profiler-cli-feedback.md)

## 143. Multi-handle `marker info --json` emits concatenated JSON documents (1)

`marker info` with several handles and `--json` emits several concatenated JSON documents instead of one array, so `json.load` fails; the agent fell back to one call per handle.

- **Question:** In what order did these markers happen, to the millisecond?
- **Command:** `profiler-cli marker info m-135 m-136 m-146 --session ...`
- **Expected:** One JSON document (array/object) for all handles with `--json`.
- **Got:** "Multi-handle `--json` output is several concatenated JSON documents, not one array, so `json.load` fails on it."
- **Suggestion:** Always emit a single JSON document.
- **Example from:** [intermittent/browser/components/places/tests/browser/browser_bookmarks_change_title.js.files/profiler-cli-feedback.md](reports/intermittent/browser/components/places/tests/browser/browser_bookmarks_change_title.js.files/profiler-cli-feedback.md)

## 144. `--during-marker` also applies `--search` as a stack search (1)

`thread samples-top-down --during-marker --search "name:Runnable,SetCharacterMap"` uses the one `--search` both as the marker filter and as a stack-frame search, so nothing matches since no frame is `name:Runnable`.

- **Question:** What was the thread doing during this one Runnable marker (SetCharacterMap)?
- **Command:** `profiler-cli thread samples-top-down --session <s> --thread t-0 --include-idle --during-marker --search "name:Runnable,SetCharacterMap"`
- **Expected:** The call tree of samples inside Runnable markers matching SetCharacterMap.
- **Got:** `Filters: [~] during marker matching: "name:Runnable,SetCharacterMap"`, then "No samples matched --search".
- **Suggestion:** A separate flag for the marker filter's search, or warn that `--search` is also applied as a stack search.
- **Example from:** [mac/dom/base/test/test_eventsource_event_listener_leaks.html.files/profiler-cli-feedback.md](reports/mac/dom/base/test/test_eventsource_event_listener_leaks.html.files/profiler-cli-feedback.md)

## 145. Zoom out-of-range warning ignores child-process data past the end (1)

`zoom push 55.0,62.5` warns the range extends past the profile duration (61.563s), while the example.org child process has markers up to 70.4 s (IPCIn ending 70.2-70.4 s, DllLoad ending 66.7 s), so zooming there is legitimate.

- **Question:** What happened in the child process after the reported profile end?
- **Command:** `profiler-cli zoom push 55.0,62.5`
- **Expected:** No warning, since child-process threads have data past 61.563 s.
- **Got:** "Range extends outside the profile duration (61.563s)".
- **Suggestion:** Compute the out-of-range bound from the latest sample/marker across all threads.
- **Example from:** [devtools/devtools/client/inspector/grids/test/browser_grids_grid-list-on-target-added-removed.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/inspector/grids/test/browser_grids_grid-list-on-target-added-removed.js.files/profiler-cli-feedback.md)

## 146. `marker info` shows no fields for IO/Memory/NetIO markers (1)

`marker info` on schema-less IO markers prints no payload fields (`write_bytes`/`read_bytes` absent, even from "Other payload fields"), and `--list --json` rows have only handle/label/start/duration.

- **Question:** What was the machine's average CPU / iowait / disk write rate over this time range?
- **Command:** `profiler-cli thread markers --session 2072588-1 --search "name:CPU Use" --list --json`
- **Expected:** Payload fields in `--list --json` rows and in `marker info`, or an aggregate over the zoom.
- **Got:** `--list --json` rows carry only handle/label/start/duration; `marker info` on an IO marker prints no fields at all.
- **Suggestion:** Print raw payload fields for schema-less markers and include them in `--list --json` rows.
- **Example from:** [intermittent/bugs/2072588.files/profiler-cli-feedback.md](reports/intermittent/bugs/2072588.files/profiler-cli-feedback.md)

## 147. profile-link.py doesn't warn when `--marker` contradicts `--search` (1)

Links built with `profile-link.py --marker m-N --search test_be_conservative` selected INFO markers that the link's own search filter hid, because per-test INFO markers do not carry the test path. profile-link.py gave no warning.

- **Question:** What did the test itself log, and do the report's links show those markers?
- **Command:** `profiler-cli thread markers --category Test --search test_be_conservative --list --limit 0 --session S`
- **Expected:** profile-link.py to refuse or warn when m-N does not match the --search X.
- **Got:** Only `load_file` and `| Starting` match the search; the links selected markers their own search hid.
- **Suggestion:** Make profile-link.py --marker m-N --search X refuse or warn when m-N does not match X.
- **Example from:** [mac/netwerk/test/unit/test_be_conservative.js.files/profiler-cli-feedback.md](reports/mac/netwerk/test/unit/test_be_conservative.js.files/profiler-cli-feedback.md)

## 148. Printed daemon foreground command uses a Node flag Node rejects (1)

When `load` of a large profile failed (daemon V8 OOM), the "foreground command" printed in the error for debugging includes `--use-env-proxy`, which the installed Node (`~/.mozbuild/node`) rejects, so it couldn't be run as printed to diagnose the crash.

- **Question:** Why does the daemon die when loading this 58 MB gzipped profile?
- **Command:** `profiler-cli load "<url>" --session marStageSuccessPartialStaleDraft.js-1`
- **Expected:** A printed foreground command that runs and shows the daemon's error.
- **Got:** `node: bad option: --use-env-proxy`
- **Suggestion:** Omit `--use-env-proxy` when the Node version does not support it; also redirect daemon stderr to its log.
- **Example from:** [mac/toolkit/mozapps/update/tests/unit_update_binary/marStageSuccessPartialStaleDraft.js.files/profiler-cli-feedback.md](reports/mac/toolkit/mozapps/update/tests/unit_update_binary/marStageSuccessPartialStaleDraft.js.files/profiler-cli-feedback.md)

## 149. `profile info --search` gives no "no match" message (1)

`profile info --search 4339` (a pid absent from the profile) prints the header and `(CPU time information not available)` with no statement that nothing matched.

- **Question:** Which process is pid 4339?
- **Command:** `profiler-cli profile info --session <s> --search 4339`
- **Expected:** The process, or "no process or thread matches 4339".
- **Got:** The header and `(CPU time information not available)`, nothing saying no match.
- **Suggestion:** Print an explicit "no process or thread matches" message.
- **Example from:** [mac/browser/components/resistfingerprinting/test/browser/browser_roundedWindow_open_max_inner.js.files/profiler-cli-feedback.md](reports/mac/browser/components/resistfingerprinting/test/browser/browser_roundedWindow_open_max_inner.js.files/profiler-cli-feedback.md)

## 150. Full view omits instant markers recorded after the last sample (1)

On a timeout profile whose samples end at 30.531 s, `thread markers --list` in the full view silently omits instant markers at 30.814/30.818 s; they appear only after `zoom push 0,32`.

- **Question:** What did the main thread run after the last sample?
- **Command:** `profiler-cli thread markers --session <s> --list --limit 0 --search "name:Runnable,-name:DummyEvent"`
- **Expected:** Every matching marker, including instants after the last sample.
- **Got:** List stops at the last sample, with nothing saying markers were left out.
- **Suggestion:** Include markers past the last sample in the full view, or print 'N markers after the last sample are outside the view'.
- **Example from:** [intermittent/dom/webtransport/test/xpcshell/test_close.js.macos-timeout.files/profiler-cli-feedback.md](reports/intermittent/dom/webtransport/test/xpcshell/test_close.js.macos-timeout.files/profiler-cli-feedback.md)

## 151. `thread info` createdAt later than the thread's first markers (1)

`thread info --json` reports `createdAt` several ms after the thread's first markers, so it can't be used as the thread start.

- **Question:** When did this worker thread start, relative to other threads' events?
- **Command:** `profiler-cli thread info --session browser_dbg-features-breakpoints.js-1 --thread t-30 --json`
- **Expected:** `createdAt` at or before the thread's first marker.
- **Got:** `createdAt: 366418.24` while first markers (`Awake`, `CompileScriptRunnable`) are at 366411.76–366414.55.
- **Example from:** [devtools/devtools/client/debugger/test/mochitest/integration/browser_dbg-features-breakpoints.js.files/profiler-cli-feedback.md](reports/devtools/devtools/client/debugger/test/mochitest/integration/browser_dbg-features-breakpoints.js.files/profiler-cli-feedback.md)

