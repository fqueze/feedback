## `zoom push` silently misparses minute-suffixed times

- Command: `profiler-cli zoom push 12m50s,24m10s --session test_contextmenu_nested.xhtml-ru` (times copied from the `t=13m7s` column of `thread markers --list` in a 26 min resource-usage profile)
- Expected: a zoom on 770 s – 1450 s, or an error saying the format is not accepted.
- Got: `Zoom depth: 1` and a view of `ts-1→ts-2 (12s)` near the start of the profile; the following `thread markers` listed startup SKIP markers as if they were the range asked for.
- Workaround: pass plain seconds (`zoom push 700,1440`). The marker list prints `13m7s`-style times, so the natural thing to paste back is not accepted.

## Default session directory not writable in the sandbox

- Command: `profiler-cli load <url> --session test_contextmenu_nested.xhtml-ru`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'` (the error message did say to set `PROFILER_CLI_SESSION_DIR`, which worked).
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call. Minor; the message was good.
