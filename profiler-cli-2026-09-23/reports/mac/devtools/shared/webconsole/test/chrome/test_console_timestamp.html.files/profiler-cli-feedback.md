## Sub-millisecond marker times (test_console_timestamp.html)

Question: "exactly when, to the microsecond, did these five markers happen?" — needed to line a
1 ms Date.now() window up against markers.

- Command: `profiler-cli thread markers --session tct-1 --list --limit 0` (inside a 3.5 ms zoom)
- Expected: times precise enough to order markers inside a millisecond, e.g. `t=2942.406ms`, at
  least when zoomed into a few ms.
- Got: `t=2.942s` for every marker in the window; ordering within the ms was lost.
- Workaround: `marker info m-a m-b ... --json` piped through a python script reading `.start`.

## Two time origins in one `marker info --json` record

- Command: `profiler-cli marker info m-330 --session tct-1 --json` after `zoom push 2.9415,2.9450`
- Expected: `start` and `context.currentViewRange` in the same time base.
- Got: `start: 2942.406` (same base as the printed `t=2.942s`), but
  `currentViewRange.start: 2949.063` and `rootRange.start: 7.563` — the view range is offset by
  the root range start. Cost time working out which one maps onto `meta.startTime`.
- Workaround: checked which interpretation matched the wall-clock values the test logged.

## Default session directory not writable in a sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session ...`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the error message
  suggested `PROFILER_CLI_SESSION_DIR`, which worked. Fine as is; noting it since the brief does
  not mention it.
