## Ordering test-log and DOM markers within one second (2026-09-22, browser_aiwindow_firstrun.js)

Question: "in what order, to the millisecond, did these markers happen?" (a test's TEST-PASS/FAIL lines vs DOMEvent / setTimeout markers 50-200 ms apart, in a 4m43s profile).

Command: `profiler-cli thread markers --category Test --search browser_aiwindow_firstrun --list --limit 0 --session <s>`

Expected: start times precise enough to order markers (ms), e.g. `t=276.251s`.
Got: `t=4m36s` for every marker in that second; `marker info` also prints `Time: 4m36s - 4m36s (43.474ms)`, and "Stack trace: Captured at: 4m35s" (which for a setTimeout marker is the time the timer was scheduled, the key fact) is rounded to the second too.

Workaround: `--list --json` and `marker info --json`, reading `start`/`end` and `stack.capturedAt` in a python one-liner. The list output could show ms (or relative-to-zoom times) once the profile is longer than a minute; `Captured at` in particular needs ms.

## "When, and by what, was each of these timers scheduled?" (2026-09-22, browser_aiwindow_firstrun.js)

Question: for every `setTimeout callback` marker matching `handleTransition`, when was it scheduled and from which caller (an auto-advance timer, a click, a promise callback)? That was the whole diagnosis: two timers scheduled by the same auto-advance.

Command: `profiler-cli thread markers --search handleTransition --list --limit 0`, then `profiler-cli marker info m-N` one by one.

Got: the list shows only the firing time (to the second) and the handler name; the scheduling time is the stack's "Captured at", visible only per marker in `marker info`, rounded to the second, with the caller several frames down.

Workaround: a python script (`timers.py` in this directory) calling `marker info --json` per marker and printing `stack.capturedAt` and a classification of the frames. For TextStack markers such as `setTimeout callback`, the `--list` row could show the capture time (ms) and the first JS frame of the stack below `Window.setTimeout`, e.g. `scheduled 273.672 by handleTransition <- handleAction <- MultiStageProtonScreen/</timer<`.

## Linking new observations from a session opened on a report's link (2026-09-22, review of browser_aiwindow_firstrun.js)

Question: "give me a link for this marker I just found, in the profile I opened from the report's link".

Command: `profiler-cli load '<profiler.firefox.com/from-url/...?marker=N&thread=0>' --session s` (as review-brief.md says), then `python3 profile-link.py --session s --marker m-5`.

Expected: a link, since the session knows the underlying Taskcluster artifact URL (it is inside the from-url link).
Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link.` for every marker, so the reviewer has to stop and reload a 0.6 GB profile to add one link. And after loading the raw Taskcluster URL, the selected thread is `t-16 (GeckoMain, WebExtensions)`, not the parent main thread, so `thread markers` silently lists another thread's markers until `thread select t-0`.

Workaround: stop, reload the raw artifact URL, `thread select t-0`, find the markers again. profile-link.py could extract the artifact URL from a from-url session; `load` of a mochitest profile could default to the parent process main thread.
