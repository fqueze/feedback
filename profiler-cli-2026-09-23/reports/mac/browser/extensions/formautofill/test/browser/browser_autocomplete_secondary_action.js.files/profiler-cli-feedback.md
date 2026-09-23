## Question: "does link `marker=N` open the marker the report quotes?" (review of a report, 54 links)

- Command: `profiler-cli marker info --session <s> m-221` (text output), then `profiler-cli marker info --session <s> m-221 --json | python3 -c '...markerIndex...'`, looped over every handle.
- Expected: the text output of `marker info` to show the marker index that profiler.firefox.com links use (`marker=N`), or a way to go from an index to a marker, e.g. `marker info --index 255212`.
- Got: the text output has no index; only `--json` has `markerIndex`. Checking each link meant finding the marker by `--search`, then a shell loop with a Python extraction per handle.
- Workaround: the loop above. A `--index N` lookup, or `profile-link.py --check <url>` printing the marker a link opens, would answer it in one call.

## `--session` before the subcommand is rejected with an unhelpful hint

- Command: `profiler-cli --session review-x-1 thread markers --category Test ...`
- Expected: accepted, or an error saying `--session` goes after the subcommand.
- Got: `error: unknown option '--session'` / `(Did you mean --version?)`.
- Workaround: moved `--session` after `thread markers`.
