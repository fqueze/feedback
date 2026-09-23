## Network marker `rawFields` times are in another timebase than `start`/`end` (review-browser_net_statistics-content.js)

- Command: `profiler-cli marker info m-148 --session review-statcontent-2 --json` (RFhkLCkj per-test profile, a `Load NNN:` Network marker)
- Question: when did this request start and end, on the same clock as the test's INFO markers?
- Expected: one set of times, or `rawFields` times labelled or shifted to the profile's zero like `start`/`end`.
- Got: `start` 152129.724 but `rawFields.startTime` 152138.189. Every Network marker is off by the same 8.465 ms, which is the `profile meta` "Uptime". In MNQ1RQ4s the offset is 7.98 ms. The report under review quoted the `rawFields` times next to the INFO markers' `start` times, so its network times were all about 8 ms late.
- Workaround: use `start`/`end` (or `thread network`) and ignore `rawFields` timestamps.
