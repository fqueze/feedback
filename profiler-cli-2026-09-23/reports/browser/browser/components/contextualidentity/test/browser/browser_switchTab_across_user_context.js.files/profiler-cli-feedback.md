## Default thread after `load` is not the parent process main thread

- Command: `profiler-cli load <Windows per-test profile URL> --session ...-3`, then `profiler-cli thread markers --session ...-3 --search "urlbar.engagement" --list`
- Expected: the parent process GeckoMain selected (as with the Linux profile of the same test, where it was t-0).
- Got: t-250 (GeckoMain, Privileged Content) selected; the query returned "No markers match" with the thread only visible in the header line, which reads like the marker does not exist.
- Workaround: `profile info | grep "Parent Process"`, then `thread select t-0`.

## Marker times past one minute lose their milliseconds

- Command: `profiler-cli thread markers --session ...-2 --category Test --search <test> --list --limit 0`
- Expected: `t=83.418s` style times, as below 60 s.
- Got: `t=1m23s` for every marker, so the order and spacing of assertions within the same second cannot be read; `marker info` also prints `Time: 1m23s`.
- Workaround: `--json` and `start/1000`.

## Question: which JS functions appear in the stacks of a set of markers

- Needed: for the 101 `SetNeedStyleFlush` markers in a 110 ms window, which UrlbarView/provider frames their stacks go through (to tell which row types were built).
- Workaround: `--json` to list handles, then one `marker stack` call per handle in a shell loop.
- What would have answered it: a `thread markers --search X --stack-functions` (aggregate of functions across the matching markers' stacks), like `samples` does for samples.
