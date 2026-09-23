## Question: which window's refresh driver ticked, and which window got this event?

- Command: `profiler-cli thread markers --search RefreshDriverTick,nativethemechange --list --limit 0 --session <id>`
- The question: in the parent process, several chrome windows each have a refresh driver; which tick and which `nativethemechange` belonged to the browser window?
- Got: rows with no window. The window is only in `marker info <handles> --json` under `rawFields[].innerWindowID`, so I scripted over it, then matched the id against a `DOMEvent` whose target is `#urlbar-input`.
- What could have shown it: the innerWindowID (or the window's document URL) as a column in `--list`, or a `--search innerWindowID:18` filter that works on raw fields.

## `marker info --json` has a different shape for one handle than for several

- Command: `profiler-cli marker info m-1 --json` vs `profiler-cli marker info m-45 m-46 m-47 --json`
- Expected: the same shape either way.
- Got: one handle returns the record itself; several return `{type, requested, markers: [...], errors, context}`. A script written against one broke on the other (`KeyError: 'markers'`).
- Workaround: handle both.

## Question: in what order did these markers happen, past t=60 s? (review)

- Command: `profiler-cli thread markers --search 'RefreshDriverTick,nativethemechange,TEST-UNEXPECTED,DoFlushPendingNotifications' --list --limit 0 --session <id>` on a profile zoomed to 60.545–60.572 s.
- The question: which came first, the browser window's tick or the test's style read? The two were 0.8 ms apart.
- Got: every row read `t=1m1s`. Past 60 s, the start column is rounded to the second, so the list cannot answer the question it exists for. Below 60 s it prints `t=57.049s`.
- Workaround: `--json`, then `marker info --json` over the handles to get `start` and `innerWindowID`.
- What could have shown it: millisecond precision whatever the magnitude (`t=60.5625s`, or `1m0.5625s`).
