## Default thread after load is not the parent main thread

- Command: `profiler-cli load <Mlub5lrOTmODOFyzr3UHXg profile_browser_multiselect_tabs_unload_telemetry.js.json> --session ...-3`, then `thread markers --category Test --search browser_multiselect_tabs_unload_telemetry --list --limit 0 --json`
- Expected: the test log markers, as with another profile of the same test, where the parent GeckoMain (t-0) was selected after load.
- Got: 0 markers, because the selected thread after load was t-203 "GeckoMain, Privileged Content". Nothing in the plain output said the thread was not the parent one unless one reads the header line.
- Workaround: `profile info --search "Parent Process"`, then `thread select t-0`.

## Question: "at what millisecond did each of these markers happen?"

- Command: `thread markers --search "eventType:TabSelect,eventType:TabBrowserDiscarded,name:Process Launch,..." --list`
- The plain list prints `t=2m35s` for every row, so markers 2 ms apart (the whole question here: did a process launch end between two events 20 ms apart) are indistinguishable. `marker info` also prints `Time: 2m35s`.
- I had to use `--json` and a script to print `start/1000` and `start+duration`. The list could print times with ms precision (e.g. `154.5968s`) and the end time of interval markers.

## (review) Same question again: "did this launch end between these two events, 20 ms apart?"

- Command: `thread markers --search "...,name:Process Launch,GetProcInfo" --list --limit 0`, in the review of this report, on all three profiles.
- Hit the same `t=2m35s` row precision as above, so again `--json` plus a script (`review-timeline.sh`, `review-windows.py`) to print `start` and `start+duration` in ms. Doing it once per profile and per question made it the main token cost of the review.
