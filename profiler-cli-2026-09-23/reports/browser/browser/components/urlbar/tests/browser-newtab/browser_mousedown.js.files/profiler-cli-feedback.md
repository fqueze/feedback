
## review (browser-review-browser_mousedown.js)

- **Question: "is there a `search.suggestions.latency` Glean timer in this profile?"** Command: `profiler-cli thread markers --search "suggestions.latency" --list --session S`. Expected: the `TimingDistribution::start` marker that the list prints as `search.suggestions.latency google:`. Got: 0 markers. The printed label joins the `cat` and `id` fields, and `--search` only matches each field value separately. Workaround: search `latency`. Either match against the printed label too, or say in the "no markers" message that the search is per field.
- Command: `profiler-cli --session S zoom push 26.7,27.2`. Got: `error: unknown option '--session'`. `--session` is accepted only after the subcommand. Expected: accepted anywhere, like `--version`.
