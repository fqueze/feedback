## Question: which interval marker starts right as another ends (chaining back-to-back RDP requests)

- Command: `profiler-cli thread markers --list --limit 0 --search "name:RDP Front" --session ...`
- Question: two interleaved chains of `target-configuration:updateConfiguration()` requests. Each request starts when the previous one in its chain ends, so telling the chains apart needs each marker's end time next to the start of the next.
- Got: start rounded to 1 ms and a duration, so I had to add them by hand; with the two chains 1 ms apart that is ambiguous. I used `--json` and a script that summed `start + duration`.
- Would have helped: an end-time column in `--list` output (or `--show-end`), at the same precision as `start` in the JSON.
