## Question: "which code dirtied style or inserted DOM in this 0.5 s window, grouped by stack?"

- Command: `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0 --json --session <s>` then a shell loop running `profiler-cli marker stack <m-N>` on each of 337 handles, then `uniq` on the joined frames.
- Expected: a way to group or search markers by their captured stack (e.g. `thread markers --search SetNeedStyleFlush --group-by stack`, or `--stack-search "promise callback"`), so the one marker whose stack is `nsCSSFrameConstructor::ContentRangeInserted <- promise callback` (the Fluent translation being applied) stands out among 300 synthesizeMouse / lit / SVG-load ones.
- Got: `--list` only shows "has stack" (a check mark); stacks need one `marker stack` call per handle (337 calls, ~1 min).
- Workaround: the loop above, output in `snsf-stacks-1.txt`.
## Question: "when was this IPC reply sent, on the same clock as everything else?" (review-browser_usage_telemetry_support_link.js)

- Command: `profiler-cli marker info m-1065 --json --session <s>` (a `PContent::Reply_FlushFOGData` IPCIn).
- Expected: `sendStartTime` / `recvEndTime` on the same time base as `start` and every `t=` the CLI prints, or labelled as a different base.
- Got: `start` is 3431.52 (printed t=3.432s) but `rawFields` `startTime`/`sendStartTime` are 3441.75, i.e. shifted by the root range start (10.23 ms here, 8.16 ms in another profile). `context.currentViewRange` is on that shifted base too, while `zoom push` takes the unshifted one. The report under review read the raw 3441.77 as "12 ms after the request"; it was 2.5 ms.
- Workaround: subtract `context.rootRange.start` by hand.

## Question: "which thread is the main one?" after loading a raw Taskcluster URL (review-browser_usage_telemetry_support_link.js)

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/.../profile_browser_usage_telemetry_support_link.js.json --session <s>`
- Expected: the parent GeckoMain selected, as when loading the profiler.firefox.com link.
- Got: `t-14 (GeckoMain, WebExtensions)` selected; the next `thread markers --search` silently returned 0 markers.
- Workaround: `thread select t-0`.
