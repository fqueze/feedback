## `thread markers --search` does not match a Glean metric name as it is displayed

- Command: `profiler-cli thread markers --session browser-context_chips-1 --search "urlbar.autocomplete,TimingDistribution::cancel" --list --limit 0` (zoomed to 9.62,10.70 on the parent main thread)
- Expected: the `TimingDistribution::start` / `::stop` markers whose row text reads `urlbar.autocomplete_first_result_time : ` (e.g. m-1430 at 10.507 s, inside the zoom), plus the cancels.
- Got: only the two `TimingDistribution::cancel` markers, i.e. the `urlbar.autocomplete` term matched nothing, although that string is exactly what the list prints.
- Workaround: `--search autocomplete_first` (no dot) found start, stop and cancel. Presumably the displayed text joins category and name with a dot, and search runs on the fields separately. Matching the displayed text, or saying so in `--help`, would have saved two queries.
- Review (2026-09-22): hit again with `--search "urlbar.autocomplete_first_result_time"` on the whole profile; `--search autocomplete_first_result_time` worked.
