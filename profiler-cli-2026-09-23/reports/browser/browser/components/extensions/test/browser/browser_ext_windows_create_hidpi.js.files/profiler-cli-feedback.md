## Question: for each window opened, how long between its show, its window-manager activation and its delayed startup?

- Command: `profiler-cli thread markers --search "xul-window-visible,browser-delayed-startup-finished,eventType:activate,domwindowopened" --list --limit 0 --session <s>` (273 rows over 29 window opens), then a Python script over the text output to pair each `domwindowopened` with the next `xul-window-visible`, `activate` and `browser-delayed-startup-finished`.
- Expected: a way to see these as per-window sequences. The markers carry `innerWindowID` for DOMEvents but not for `NotifyObservers`, so the list cannot be grouped by window.
- Got: one flat chronological list; pairing had to be done by position.
- What would have answered it: `--group-by field:innerWindowID` working across marker types (NotifyObservers subjects resolved to their window), or a per-window lifecycle summary.
