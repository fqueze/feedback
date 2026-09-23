## `thread network --sort start` prints no start times

- Command: `profiler-cli thread network --session <s> --sort start --limit 0`
- Expected: each request's start/end time, since the question was "was this request in flight when X ran?".
- Got: URL, status, duration and phases, but no timestamps; I had to run `marker info m-N` per request and read `Time:`.
- Workaround: `profiler-cli marker info m-1 m-2 | grep ^Time`.
