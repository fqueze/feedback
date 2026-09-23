## `zoom push` silently accepts the times the tool itself prints, and zooms to 1 ns

- Command: `profiler-cli zoom push --session <s> 9m20s,9m40s` (the marker list prints times as `t=9m34s`), then `thread markers ... --list`.
- Expected: a 20 s zoom, or an error saying only seconds are accepted.
- Got: `Zoom depth: 1`, then `View: ts-1→ts-2 (1.000ns)` and "No markers match", with no warning.
- Workaround: `zoom push 560,580`.

## A `--search` term containing `: ` silently becomes a field filter and matches nothing

- Command: `profiler-cli thread markers --search 'SANITY_TEST,sanity-test.running: true,ABNORMAL' --list`, and the same with `hardware-video-decoding.failed: true`.
- Expected: substring match on the marker text as printed (`sanity-test.running: true (Bool)`).
- Got: the `...: true` terms matched nothing, silently, while the other terms matched. It reads as "this pref was never written".
- Workaround: search `sanity-test.running` and filter the list by eye. A warning like "`sanity-test.running` is not a field of any marker in this thread" would have caught it.

## (review) Session names that follow the brief's convention hit the Unix socket path limit

- Command: `PROFILER_CLI_SESSION_OWNER=review-test_gfx_sanity_gpu.html.PassedNoHardwareEncoder profiler-cli load --session review-test_gfx_sanity_gpu.html.PassedNoHardwareEncoder-sKpwlR_WhQFWCLvsTnMWekw <url>`
- Expected: a session. The review brief asks for `review-<report name>` in session names, and adding a task ID is the natural way to tell profiles apart.
- Got: `The Unix socket path for this session is 112 bytes, over this platform's 107-byte limit`. The message is clear, but a scripted loop that redirects output fails silently, one profile after another.
- Workaround: short numeric suffixes. The socket file could be named from a hash of the session name, so that name length no longer matters.
