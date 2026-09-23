Need a script over --json: CPU Use stats over a zoomed range (question: was the machine saturated during this wait?). Command: profiler-cli thread markers --search 'name:CPU Use' --list --json after zoom push 378.8,384.2. The default output lists one row per 100ms marker without cpuPercent; an aggregate (min/avg/max of cpuPercent) in the default marker summary would answer it.

## Question: was the machine saturated during a 5 s wait? (resource-usage profile)

- Command: `profiler-cli zoom push 378.8,384.2` then `profiler-cli thread markers --search "name:CPU Use"` (and `--list`).
- Expected: the CPU percentage, or min/avg/max of `cpuPercent` over the zoomed range.
- Got: only the marker count and durations (`CPU Use 55 markers (interval: min=99ms…)`); `--list` rows show no `cpuPercent` either.
- Workaround: `--list --limit 0 --json` piped to a Python one-liner averaging `data.cpuPercent`.

## (review, browser-review-927717) Same question again: was the machine saturated during a 5 s wait?

- Command: `profiler-cli zoom push 378.842,384.12 --session …` then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json --session …`.
- Expected: min/avg/max of CPU percent over the zoomed range, in the default output.
- Got: needed a script again; in the JSON, markers are under `flatMarkers`, and `cpuPercent` is a string with a unit (`"35.0%"`), so it has to be parsed before averaging.
- Workaround: Python over `flatMarkers[].fields[key=cpuPercent].value.rstrip('%')`.

## (review) Global `--session` before the subcommand is rejected

- Command: `profiler-cli --session browser-review-927717-1 thread markers --search browser_927717 --list --limit 0`
- Expected: same as putting `--session` after the subcommand.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand.
