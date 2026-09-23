## Daemon died on `thread samples-top-down --include-idle` over a 30 ms zoom

- Command: `profiler-cli zoom push 56.570,56.600 --session browser_ext_url_overrides_newtab.js-1` then `profiler-cli thread samples-top-down --include-idle --session browser_ext_url_overrides_newtab.js-1` (thread t-34, GeckoMain of the WebExtensions process, in `profile_browser_ext_url_overrides_newtab.js.json` of task SS2FWXH5SxikPdCVYG0WHw).
- Expected: a call tree, or "no samples in range".
- Got: `Error: Timed out after 30000ms waiting for the daemon`, then the session was gone ("The daemon exited without cleaning up"). The log ends on `Received message: command` with no error. Just before, the same command over 56.580,56.596 printed an empty "Top-Down Call Tree:" with no message saying there were no samples in the range.
- Workaround: reload the profile; every marker handle obtained so far was lost, so observations had to be re-derived before linking.

## Times over one minute print as `1m44s`, which cannot place markers against each other

- Command: `profiler-cli thread markers --category Test --search browser_ext_url_overrides_newtab.js --list --limit 0 --session browser_ext_url_overrides_newtab.js-3` (task PCApicX1R5mH4SConQXx8g, a 1m45s profile), and `profiler-cli marker info m-118 m-123 m-11`.
- Question: in what order, to the millisecond, did the test's last steps happen?
- Expected: `t=104.113s` or `t=1m44.113s`, as the list does for profiles shorter than a minute.
- Got: `t=1m43s` / `t=1m44s` for every row, and `Time: 1m44s (instant)` in `marker info`, so ten markers within one second all read the same.
- Workaround: `marker info --json` and the `start` field (ms), or `zoom push` with seconds (`103.3,104.2`), which does accept plain seconds.

## IPC payload times are on another clock than the marker's own time (review)

- Command: `profiler-cli marker info m-4 --session review-newtab-3` (IPCIn `PBrowser::Msg_Destroy` on the WebExtensions main thread, task PCApicX1R5mH4SConQXx8g), and the matching parent IPCOut `m-119`.
- Question: when was this message sent, on the clock every other marker uses?
- Expected: `startTime`, `sendStartTime`, `recvEndTime` on the same zero as the marker's `Time`, or labelled as raw.
- Got: `Time: 103.999s` but `startTime: 104009.806653`, `sendStartTime: 104009.819803` — 10.2 ms later (7.6 ms in task SS2FWXH5SxikPdCVYG0WHw), on both the parent and the child side, under "Other payload fields (no schema)". Read as-is, the send lands after markers it actually precedes.
- Workaround: use only differences between payload fields (send-to-receive latency), and the marker's `Time` for ordering.
