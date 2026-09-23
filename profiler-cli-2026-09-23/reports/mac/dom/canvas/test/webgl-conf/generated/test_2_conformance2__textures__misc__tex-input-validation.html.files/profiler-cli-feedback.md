
## review-tex-input-validation (2026-09-22) — "which handle is the marker this link points at?"

- Command: `profiler-cli load '<profiler.firefox.com link ...marker-table/?marker=29520&thread=0&v=17>' --session review-tex-input-validation-1`
- Expected: the load output (or `status`) to name the handle of the link's selected marker, e.g. `Selected marker: m-86 (index 29520)`.
- Got: only thread/range/filters. To check each of a report's ~20 links I had to `thread markers --search` for the quoted text, then `marker info <m-…> --json` per candidate to compare `markerIndex`. Passing several handles to `marker info --json` in one call also did not give a list I could index the same way as a single call.
- Workaround: one `marker info --json` call per handle in a shell loop.
- Could have shown: a `marker info --index 29520` (or accepting the link itself) that resolves an index to its handle directly.

Also: `profiler-cli --session X profile info` fails with `unknown option '--session'`; the option is only accepted after the subcommand. Cost one call.
