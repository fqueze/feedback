
## review-browser_tab_manager_groups.js: ordering markers within the same millisecond

- Question: in what order do `L10nMutationsFinished`, the `LocalizationRc::format_values` re-poll, `Perform microtasks`, the `RefreshObserver` and `nsXULPopupShownEvent` run? All of them are at "t=6.910s"–"t=6.912s".
- Command: `profiler-cli thread markers --session <s> --list --limit 0 --search '...'` after `zoom push 6.9095,6.9125`.
- Expected: start times precise enough to order them (µs), or at least list order that is chronological within one ms.
- Got: times rounded to 1 ms, so all of them show the same value. Getting the order meant a Python script over `--json` `flatMarkers[].start`.
- Also: after `load <taskcluster raw URL>`, the selected thread was t-23 (Privileged Content) and not the parent main thread that a from-url link with `thread=0` selects. The first marker query came back empty without saying why. Workaround: `thread select t-0`.
