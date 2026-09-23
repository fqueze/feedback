## Question: "which document was each Reflow for, and which document is innerWindowID N?"

- Command: `profiler-cli thread markers --search "name:Reflow" --list --limit 0 --session browser-chats-2`
- Expected: each Reflow row showing its document (innerWindowID, ideally its URL, e.g. `chrome://browser/content/browser.xhtml` vs `about:firefoxview`).
- Got: only `Reflow (sync)` / `Reflow (interruptible)` with a duration. innerWindowID is only in `marker info` or `--json`, and no command maps innerWindowID to a page URL (the profile's `pages` table). I had to use `--json` plus a Python filter on `data.innerWindowID`, and work out which window was browser.xhtml from an `ai-window:sidebar-toggle - window` DOMEvent.
- Could have shown: the page URL (from `pages`) next to each marker that carries an innerWindowID, in list output. Also a `profile pages` command listing innerWindowID -> URL -> tab/window.

## Question: "what happened on this thread during these 30 ms?" (review of this report)

- Command: `profiler-cli zoom push 4.305,4.335 --session S` then `profiler-cli thread markers --session S --list --limit 0 --search "-name:RefreshDriverTick"`
- Expected: the markers that start inside the 30 ms window, in order.
- Got: 873 rows, the first ~150 of them long-running markers that started seconds earlier and merely overlap the window (IPC pairs, CSS animations, AsyncShutdown blockers, BHR hang, CompositorScreenshot), burying the dispatch sequence. Workaround: `--json` and a Python filter on `start >= zoom start`.
- Could have shown: an option to list only markers starting in the view range (or to drop markers longer than the range), or put overlapping-from-before markers in a separate collapsed group.

## `--session` is rejected before the subcommand

- Command: `profiler-cli --session S thread markers ...`
- Expected: same as `profiler-cli thread markers --session S ...`.
- Got: `error: unknown option '--session' (Did you mean --version?)`. Workaround: put `--session` after the subcommand.
