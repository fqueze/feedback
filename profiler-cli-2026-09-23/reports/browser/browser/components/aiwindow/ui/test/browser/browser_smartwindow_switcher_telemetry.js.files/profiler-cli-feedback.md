## Question: how many parent-process DOM windows were alive during each test?

- Command: `profiler-cli thread markers --session <s> --search "name:DOMWindow" --list --limit 0 --json`, then a Python script (`alive.py` here) intersecting the DOMWindow intervals of one pid with each `test` marker.
- What the output could show: per `test` marker, the number of DOMWindow/DocShell interval markers alive at its start, peak and end, per process. This is the leak/accumulation question for any long browser-chrome session, and the resource-usage profile holds all it needs.
- Also: DOMWindow markers whose window was never destroyed (crashed process) are instant markers without a `duration` in the JSON, so a script has to guess they last until the process ends.
