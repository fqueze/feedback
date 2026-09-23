## Question: in what order did two markers a few ms apart happen?

Command: `profiler-cli thread markers --category Test --search browser_tab_groups_list.js --list --limit 0 --session S`
(and the same with `--search "eventType:TabOpen,...,XULFrameLoaderCreated"`)

Expected: a time column precise enough to order markers in a 7-minute profile (e.g. `t=424.0653s`).
Got: `t=7m4s` on every row: the assertion, the TabOpen, the popupshown and the IPC all print the
same second. `marker info` also prints `Time: 7m4s (instant)`.
Workaround: `--json` piped to a python script printing `start` in ms. Needed on every listing of
this investigation; the whole diagnosis is a 2 ms race.
What could have shown it: millisecond precision in `--list` rows and `marker info`, at least once
the profile is longer than a minute (or when a zoom narrower than 1 s is active).

## Question: which IPC message is this IPCIn marker?

Command: `profiler-cli thread markers --session S --search IPCIn --list`
Expected: the message type in the row, like Runnable rows show `PBrowser::Msg_OnLocationChange`.
Got: rows labelled `IPCIn IPCIn` only; the type is in `marker info` or `--json` `data.messageType`.
Workaround: `--json` and a script printing `data.messageType`.

## IPC payload times are in another time base than the marker's own time

Command: `profiler-cli marker info m-115 --session browser_tab_groups_list.js-2`
Got: `Time: 4.666s - 4.686s`, but under "Other payload fields (no schema)": `startTime: 4676.10`,
`sendStartTime: 4676.105`, `recvEndTime: 4676.12`, `endTime: 4696.71` — 10.2 ms later than the
marker's displayed range, while the child's matching IPCOut is at 4665.9 in the displayed base.
In another profile the offset was 0.27 ms. Reading `recvEndTime` against the displayed times of
other markers gives the wrong order.
Expected: the payload times shifted into the same zero as the displayed ones, or flagged as raw.
Workaround: ignore the payload times and use the marker's displayed start.

## (review) Question: which thread does a link's `thread=B4` open?

Command: checking a report link `.../marker-table/?marker=332514&thread=B4` while the session had loaded the same profile's `thread=0` link.
Expected: a way to map the URL's thread parameter to a handle in an already-loaded session, e.g. the URL-encoded thread index in `thread list` / `profile info`, or `thread select B4`.
Got: none; checking links on a second thread of the same profile needs a second 0.6 GB load, and `profile-link.py` refuses a session loaded from a profiler link ("load the Taskcluster URL itself"), which is what the review brief says to load.
Workaround: decoded `B4` by hand (continuation digit `B`=5, final `4` → 5*32+4 = 164 = t-164) and checked `marker info --json` reported `threadHandle: t-164` for markerIndex 332514.

## (review) `marker info --json` changes shape with the number of handles

Command: `profiler-cli marker info m-1 --json` vs `profiler-cli marker info m-1 m-2 --json`
Got: a single marker object for one handle; `{type, requested, markers: [...], errors, context}` for several. A script written against one breaks on the other.
Expected: the same shape either way (always `markers: [...]`), or documented in `schemas`.
