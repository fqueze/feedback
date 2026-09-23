
## Which marker does a link's `marker=N` point to? (review of browser_override.js)

- Command: `profiler-cli marker info m-58 --session <s>` (and `thread markers --list`), to check that a report's link `...?marker=363530` is the marker it quotes.
- Expected: the marker index (the `marker=N` of a profiler.firefox.com link) printed in the plain output, or `load <link>` printing which marker the URL selects.
- Got: neither shows it; only `marker info --json` has `markerIndex`, so checking a dozen links took a python loop over `marker info m-a m-b ... --json`.
- Suggestion: print `Index: N` in `marker info`, and have `load` report the URL's selected marker as a handle.

## `thread markers --list` hides the target of `CSS transition` markers

- Command: `profiler-cli thread markers --session <s> --list --search "CSS transition"`
- Expected: which element each transition ran on; the payload has a `Target` field (`tab@32e840220 class="tabbrowser-tab"`), and `--search <address>` does match on it.
- Got: only the property (`max-width`), so two tabs' transitions look alike until `marker info` on each.
- Suggestion: include `Target` in the one-line description of `CSS transition` / `CSS animation` markers.
