# profiler-cli feedback (browser_sync.js)

## Timestamps past one minute lose all sub-second precision

- Command: `profiler-cli thread markers --category Test --search browser_sync.js --list --limit 0 --session ...` on a 2m24s profile.
- Expected: `t=80.875s` or `t=1m20.875s`.
- Got: `t=1m`, `t=1m3s`, `t=2m`, and `marker info` gives `Time: 1m21s`. Below 60 s the same list shows `t=58.263s`. The gaps between steps of a slow test can't be read from the list, and consecutive markers look simultaneous.
- Workaround: `marker info m-N --json` and read `start` (80874.98 ms) for each marker I needed.

## Question: which markers' stacks go through function X (review, browser-review-browser_sync.js)

- Command: `thread markers --search name:SetNeedStyleFlush --list --limit 0` over t=5.5-37.08s (5,503 markers), then `marker info <500 handles> --json` in batches and a script over `stack.frames` to find any going through `MenuMessage` or `onFxaDisabled`.
- What the output could have shown: a stack filter on `thread markers`, e.g. `--stack-search MenuMessage`, listing only the markers whose stack contains that frame. That answers "did anything dirty style from this code path in this window", which is how you rule out another writer of an attribute.

## Question: the average machine CPU use over a range (review, browser-review-browser_sync.js)

- Command: `thread markers --search "CPU Use"` in a zoomed resource-usage profile. The aggregate view gives the count and interval durations but not the `cpuPercent` values. I had to list the handles, run `marker info --json` on them, and average `fields[cpuPercent]` in a script, once per range.
- What the output could have shown: the mean and p90 of numeric payload fields in the aggregate view, or `CPU Use` as a counter (`counter info` over the zoom).
