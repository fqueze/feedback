
## review-1809667.macos-vms: `--group-by` with a bare payload key groups silently into "(no value)"

- Question: how many DOMEvent markers of each event type does the parent main thread have?
- Command: `profiler-cli thread markers --search "name:DOMEvent" --group-by eventType --session <s>`
- Expected: groups by the `eventType` payload field, or an error pointing to `field:eventType`.
- Got: one group, `(no value): 10476 markers`, with no hint that `eventType` was read as a marker-level key.
- Workaround: `--group-by "field:eventType"`, found in `thread markers --help`.
