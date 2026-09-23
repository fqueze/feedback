## Question: "at what exact time (ms) did these test log markers happen?"

- Command: `profiler-cli thread markers --category Test --search <test> --list --limit 0` and `profiler-cli marker info m-24 m-30 ...` on a 24-minute profile.
- Expected: timestamps precise enough to order events and zoom (e.g. `22m45.611s` or `1365.611s`).
- Got: `t=22m44s` / `Time: 22m44s` - whole seconds only. Ten markers in the same second are indistinguishable, and `zoom push` needs sub-second values.
- Workaround: `--json` and read `flatMarkers[].start`.
