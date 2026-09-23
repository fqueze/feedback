## Machine CPU during one test needs --json and a script

- Question: was the machine saturated while this test ran (resource-usage profile)?
- Command: `profiler-cli zoom push m-1 --session S` then `profiler-cli thread markers --session S --search "name:CPU Use" --list --limit 0 --json`, averaging `cpuPercent` in Python.
- Could have shown: the `thread markers` aggregate for `CPU Use` could give mean/median/max of the CPU Percent field within the zoom, or `counter`-style stats for these markers.

## zoom push does not take two marker handles

- Command: `profiler-cli zoom push m-190,m-201 --session S`
- Expected: zoom from the start of m-190 to the end of m-201, the way `ts-6,ts-12` works.
- Got: `Error: Invalid time value: "m-190". Expected timestamp name (ts-X), seconds ...`
- Workaround: seconds.

## --include-idle header still says "running samples"

- Command: `profiler-cli thread samples --session S --include-idle`
- Got: `Categories (1564 running samples)`, identical to the same command without `--include-idle`, so I could not tell whether idle samples existed and were counted. I had to compare the sample count with `thread info` to see there was no idle in that window.
