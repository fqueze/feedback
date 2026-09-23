## Question: was the machine saturated while one test ran?

- Command: `profiler-cli zoom push m-3` on a resource-usage profile, then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json | python3 ...` to average the `cpuPercent` field.
- Expected: a summary of CPU Use over the zoomed range (mean / max / time above 90%), e.g. from `thread markers --search "name:CPU Use"` aggregate output or a `counter`-like view; `counter list` says "No counters in this profile".
- Got: only per-marker values; the aggregate shows duration stats, not the payload value. The value is a string ("34.1%"), so scripts must strip the `%`.
- Workaround: the python script above.

## Question: what is the call pattern of a marker flood (how many B per A)?

- Command: `profiler-cli zoom push 121.1035,121.4469 --session S; profiler-cli thread markers --session S --list --limit 0 --json | python3 ...` (a ~340ms range holding ~1.7M markers) to run-length-encode `DocAccessible::PruneOrInsertSubtree` vs `DocAccessible::ContentRemovedNode`.
- Expected: an answer within a minute, or an aggregate such as "markers of name B between consecutive markers of name A" / a run-length view of `--list`.
- Got: the command ran for 10 minutes and was killed by the tool timeout; the zoom stayed pushed (the `zoom pop` after it never ran).
- Workaround: zoom to 3-4ms windows, `--list --limit 0` as text, then `awk '{print $2}' | uniq -c`. That showed the answer at once (1 PruneOrInsertSubtree, then exactly 865 ContentRemovedNode, repeated).

## `--search -category:Accessibility` does not exclude by category

- Command: `profiler-cli thread markers --search -name:DocAccessible::ContentRemovedNode,-name:DocAccessible::PruneOrInsertSubtree,-name:Preference\ Read --min-duration 100 --list` worked, but `--search -category:Accessibility,-name:Preference\ Read --list` still listed Accessibility markers.
- Expected: `-category:` to exclude a category, as `--category` includes one.
- Workaround: exclude each marker name.

## Question: what time range does a flooded buffer still cover?

- The profile says `Full: 2m19s`, but the parent main thread only holds data from its last ~8s (samples only in the last 1.8s) because 7.1M a11y markers overwrote the buffer. I found out from `thread info` ("192 samples") and by listing the first markers. `profile info` could say "oldest retained sample/marker on this thread: t=..." so a truncated profile is not read as a full one.

## `flatMarkers[]` in `thread markers --list --json` have no `end`

- `m['end']` is absent; I had to compute `start + duration`. `marker info --json` has `end`.
