## `thread markers --list` rounds times to whole seconds

- Command: `profiler-cli thread markers --session <s> --search DOMWindow,DocShell --list --limit 0` (after `zoom push 634.0,651.0`)
- Question: in what order, within about 4 s, were the about:support windows destroyed, relative to the harness's "Completed ShutdownLeaks collections" lines and the leak-path-finder start?
- Expected: start (and, for interval markers, end) times with ms resolution, since `zoom push` is narrower than a minute.
- Got: `t=10m34s` for every row and a duration. Ordering events inside one second, or getting an interval's end, was impossible from the text.
- Workaround: `--json`, plus a script adding `start + duration` and sorting creates/destroys with the log lines into one timeline.
- What the output could have shown: ms-resolution start (relative to profile start) once the view is under a few minutes, and an `end` column for interval markers.

## `marker info --json` and `thread markers --json` name the handle differently

- `marker info --json` records use `markerHandle`; `thread markers --list --json` rows use `handle`. My script, written against one, broke on the other (KeyError). Minor.
