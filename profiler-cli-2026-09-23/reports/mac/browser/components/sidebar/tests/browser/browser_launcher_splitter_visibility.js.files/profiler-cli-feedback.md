## Question: exact times of markers a few ms apart, in a profile over a minute long

- Command: `profiler-cli thread markers --category Test --list --limit 0` (and `profiler-cli screenshots --range ...`)
- Expected: millisecond times, since the pref write, the failing assertion and the next refresh tick were 0.3 to 22 ms apart.
- Got: every row printed as `t=1m11s`.
- Workaround: `--json` and read `start`. The text output could keep ms precision (e.g. `t=71.339s`) once the profile passes a minute.

## Question: which window (document) does a marker belong to?

- Command: `profiler-cli thread markers --search "name:RefreshDriverTick,nativethemechange" --list`
- Expected: the innerWindowID, which is the only way to tell the main window's refresh tick from another window's.
- Got: not shown in the list; only in `marker info --json` `rawFields`.
- Workaround: `xargs profiler-cli marker info --json` over the handles. An inner-window column (or a `--window <id>` filter) would answer it directly.

## Question: which markers in a time range have a given JS function on their stack?

- Command: `profiler-cli thread markers --has-stack --list` then `profiler-cli marker info <handles> --json`
- Expected: a way to filter markers by stack frame (e.g. every SetNeedStyleFlush whose stack goes through `toggleExpandOnHover`).
- Got: `--search` matches names/payloads only. `marker info` refuses ranges over 256 handles ("covers 968 handles, more than the maximum of 256"), and under zsh a single `$H` of handles is taken as one argument ("Invalid marker handle m-3249 m-3250 ...").
- Workaround: split the handle list into chunks of 200, `xargs` each into `marker info --json`, grep the frames in Python. 16k markers on the Mac profile took minutes. A `--stack-search <fn>` on `thread markers` would answer it.

## (review) A zoom range outside the profile is accepted silently

- Command: `profiler-cli zoom push 71313.5,71340 --session review-lsv-1` (ms typed where seconds are expected; the profile is 71.8 s long)
- Expected: an error saying the range lies outside the profile (0 to 71.77 s).
- Got: `View range: ts>10X to ts>10y (26.500s)`, and every following `thread markers` query answered "No markers match the specified filters", which reads as a real finding.
- Workaround: `zoom clear`, then `zoom push 71.3135,71.340`.
