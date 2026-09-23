# profiler-cli feedback — browser_policy_search_engine.js

## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster URL> --session browser_policy_search_engine.js-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, then `Unknown session` on the next command.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command (the error message suggested it). One wasted round trip; the brief does not mention the variable.

## Question: "which `resize` events targeted a window, over the whole profile?"

- Command: `profiler-cli thread markers --search "name:DOMEvent,eventType:resize" --list --limit 0` (on a 1.7M-marker parent main thread)
- Expected: an AND of the two field filters, i.e. only resize DOMEvents.
- Got: comma is OR (as documented), so it listed every DOMEvent; it ran past the 120s Bash timeout and had to be backgrounded, then piped through `rg "resize - window"`.
- What would have answered it: an AND operator for marker search (or `--search` given twice meaning AND), so field filters can be combined.

## Question: "does marker B happen inside marker A's dispatch?"

- Command: `profiler-cli marker info m-27 m-152 m-153 m-287 --json | python3 ...` to read `start`/`end`.
- Needed because the default `--list` and `marker info` print times rounded to the millisecond (`t=40.401s` for a dozen markers within 1ms), so nesting and ordering inside one event dispatch cannot be read from the text output.
- What could have shown it: sub-millisecond start/end in `marker info` text output (it already prints the duration in μs), or more digits in `--list` when zoomed into a range shorter than a few ms.
