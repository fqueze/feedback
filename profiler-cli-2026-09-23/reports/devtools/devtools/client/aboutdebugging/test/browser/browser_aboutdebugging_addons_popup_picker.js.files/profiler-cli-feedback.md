## Question: "at what time, to the millisecond, did each marker after the first minute happen?"

- Command: `profiler-cli thread markers --session <s> --category Test --search popup_picker --list --limit 0` (and `marker info m-186`)
- Expected: `t=59.905s`-style timestamps, as for markers before 60 s.
- Got: every marker past 60 s prints `t=1m`, and `marker info` prints `Time: 1m - 1m`, so the order and spacing of a test's steps (all at 59.7-60.8 s here) cannot be read.
- Workaround: `--json` and a script printing `start/1000` with three decimals. The text output could print `t=1m0.031s` or plain seconds.

## Question: "which runnable or event-loop task was this marker dispatched in?"

- Command: `profiler-cli thread markers --session <s> --list --limit 0` zoomed on a few ms around a `popuphiding` DOMEvent, to tell whether it ran inside a queued Runnable, a microtask checkpoint or a native (widget) event.
- Expected: some way to list the interval markers that enclose a given instant or marker (e.g. `marker info m-155 --enclosing`).
- Got: the zoomed list includes long intervals starting before the zoom, mixed with everything else. I had to script over `--json` to keep the markers whose `[start, start+duration]` contains the instant.
- Workaround: script over `--json`.


## Question: "find the markers whose listed text is `activate - window`" (review)

- Command: `profiler-cli thread markers --session <s> --search "Open the add-on popup,activate - window,popupshown,popuphiding" --list --limit 0`
- Expected: the `deactivate - window` / `activate - window` DOMEvent rows, since that is the text `--list` prints for them.
- Got: only the other terms matched. The printed label (`<eventType> - <target>`) is not searched, only the payload fields, so the text from one listing cannot be used to search.
- Workaround: `--search "eventType:activate,eventType:deactivate"`. The search could also match the printed label.
