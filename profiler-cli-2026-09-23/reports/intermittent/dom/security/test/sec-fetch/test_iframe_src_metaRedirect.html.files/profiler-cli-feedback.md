## Question: "how far apart are these two markers"

- Command: `profiler-cli thread markers --session <s> --search sec-fetch --list --limit 0`
- Expected: timestamps precise enough to tell apart events tens of ms apart (e.g. `t=263.872s`).
- Got: `t=4m24s` for every row across a 150 ms window, so the 44 ms gap between the two `?pageC` notifications, the key observation, was invisible in the default output.
- Workaround: `--json` piped to a python one-liner printing `start`.
