## Question: which DOM events happened between two test log lines, in order, to the millisecond

- Command: `profiler-cli thread markers --session test_panel.xhtml-1 --search DOMEvent --list --limit 0` (after `zoom push 426.80,426.95`), in a 7m7s profile.
- Expected: timestamps precise enough to order a `mousedown` against the `TEST-UNEXPECTED-FAIL` 1 ms later.
- Got: every row prints `t=7m7s`; the list is sorted, but nothing says which marker precedes the failure when two searches have to be combined. Combining `DOMEvent` with the Test category also needs two calls: a bare `Test` term matches hundreds of `Preference Read` markers whose pref names contain "test".
- Workaround: `--json` for both searches, a script printing `start` in ms, and `sort`.
- What the default output could show: millisecond (or relative-to-zoom) timestamps once the view is zoomed below a few seconds, and a way to OR a name with a category in one call.

## `screenshots --range` lists each window's frames, but `thread markers --search CompositorScreenshot --json` has no windowID in `fields`

- Command: `profiler-cli thread markers --session test_panel.xhtml-1 --search CompositorScreenshot --list --limit 0 --json`
- Expected: `windowID`, `windowWidth`, `windowHeight` in each flat marker's `fields`, as `marker info` shows them.
- Got: empty fields; `end` missing as well.
- Workaround: `screenshots --range`, which prints the window per frame, then `marker info` for times.

## Question (review): was this window still open at time t?

- Command: `profiler-cli screenshots --at 426.8775 -o <dir> --session review-test_panel.xhtml-1`
- Expected: each window on screen at t, or a flag on a window whose last frame is old, saying whether a `CompositorScreenshotWindowDestroyed` ended it.
- Got: `m-116 t=6m50s win 2 200px × 200px` listed with the others, although its last frame was 17 s earlier. Nothing says whether win 2 still existed. The destroyed markers are labelled just `CompositorScreenshotWindowDestroyed` in `thread markers --list`, and their `windowID` only appears in `marker info` under "Fields (raw payload, no schema)".
- Workaround: `marker info m-116..m-160 --json` over all 45 destroyed markers, plus a script reading `rawFields[0].value`. Window 2 was never destroyed.
- What the default output could show: the `windowID` in the destroyed marker's label, and in `screenshots --at`, "last frame 17.2 s before t, window not destroyed" next to stale entries.
