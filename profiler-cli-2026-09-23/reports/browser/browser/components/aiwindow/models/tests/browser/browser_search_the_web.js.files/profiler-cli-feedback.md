## Times over one minute print as "1m5s", losing the precision needed to order events
- Command: `profiler-cli marker info m-943 m-16 --session <s>` and `thread markers --list` (profile of 1m9s)
- Expected: start/end to the millisecond, as for times under a minute (`t=57.587s`).
- Got: `Time: 1m5s - 1m5s (7.131ms)`, `t=1m3s`: could not tell whether DOMContentLoaded came before or after the test's failure, 2.5 s apart.
- Workaround: `marker info ... --json` and read `start`.

## Question: "when was each process launched, and when did it exit, in seconds?"
- Command: `profiler-cli profile info --all --session <s>`
- Got: `[ts-o → ts-O]` timestamp handles per process, not times; to line process launches up with the test's markers I scripted over `profile info --json`.
- Could have shown: the start/end in seconds next to (or instead of) the ts handles.

## Question: "what were the machine's CPU and memory over this window?" (resource-usage profile)
- Command: `profiler-cli counter list` -> "No counters in this profile."; `thread markers --search "CPU Use" --list` lists the markers without their values.
- Got: the values only in `marker info` one by one, or `--json`; I scripted over `thread markers --search "name:CPU Use,name:Memory" --list --json`.
- Could have shown: CPU % / system % / memory used as a time series (like `counter info`'s "over time" buckets) for a zoomed range.

## `thread list` CPU column disagrees with `thread info` and `Thread CPU use` (review, profiler-cli 0.9.0)
- Command: `profiler-cli thread list --session <s>` on the c_xTNhEERjiEr0e1hTdhTA per-test profile.
- Expected: about 6.7 s for `t-58` (pid 7308 GeckoMain). Its `Thread CPU use` marker says `6,738ms of CPU time`, and `thread info` zoomed on its 10.1 s xul.dll load says `100% for 10133.5ms`.
- Got: `211.347ms` (the same figure in `profile info`). For pid 8992, 470 ms against 8,281 ms.
- Workaround: trust `thread info` / `Thread CPU use`, not the list column. It nearly made me doubt "the stall is CPU work".

## `thread markers --search` does not match the text it prints for Subprocess Priority
- Command: `profiler-cli thread markers --search "priority of child" --list --session <s>`
- Expected: the `priority of child 8,992: PREALLOC -> FOREGROUND` rows it prints.
- Got: 0 markers. The label is built from the `pid`/`Before`/`After` fields, and those are what `--search` matches.
- Workaround: `--search "Subprocess Priority"` or `--search PREALLOC`.
