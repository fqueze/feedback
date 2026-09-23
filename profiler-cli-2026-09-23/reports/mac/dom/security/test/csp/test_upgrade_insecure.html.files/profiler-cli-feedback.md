## test_upgrade_insecure.html (2026-09-22)

- **Command:** `profiler-cli thread network --sort start --limit 0` (session on PE4UBc2VSYuzwAVa7nrPSw, parent main thread, zoomed 21.3,22.0)
  **Expected:** a failed request's line to say how it failed.
  **Got:** `???  cache=Unresolved  duration=6.605ms` — the payload's `requestStatus: NS_ERROR_NET_RESET` is only visible through `marker info`.
  **Workaround:** `marker info m-66`.

- **Command:** same.
  **Expected:** phase durations that are non-negative, or a note on why not.
  **Got:** every request through the HTTPS proxy (CONNECT tunnel) prints `TLS=-25,500ns`, `TLS=-1,357,960ns` in the summary; `secureConnectionStart` falls after `connectEnd` for tunnelled connections. Reads like a tool bug.
  **Workaround:** ignored the TLS phase.

- **Question:** "which connections to the same host were in their TCP connect at the same moment as the failing one?"
  **Command:** `profiler-cli marker info m-60 m-61 m-63 ... | rg 'connectStart|tcpConnectEnd'`
  **What would have answered it:** `thread network` printing each request's absolute `connectStart`/`tcpConnectEnd` (or a per-phase timeline) instead of only phase durations; the durations cannot be lined up against each other. Also, the payload times (`startTime: 21770.17`) are on a different zero than the displayed `t=21.747s` (about 23 ms apart), unexplained.

- **Command:** `profiler-cli load <url> --session ...` in a sandboxed agent shell.
  **Got:** `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The message named the fix (`PROFILER_CLI_SESSION_DIR`), so cheap, but every agent here pays it once.
