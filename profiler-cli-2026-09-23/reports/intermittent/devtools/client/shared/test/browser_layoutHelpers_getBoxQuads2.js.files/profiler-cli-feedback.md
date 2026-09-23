## Question: the millisecond order of markers in the last second of a long profile

- Command: `profiler-cli thread markers --category Test --search browser_layoutHelpers_getBoxQuads2 --list --limit 0 --session <s>` (and the same with `--search 'occlusionstatechange,...'`) on a 1m45s profile (task OsQWrmPOTdip82mvMp5D2w).
- Expected: timestamps precise enough to order events a few ms apart, as the output does on a short profile (`t=2.504s`).
- Got: every row printed `t=1m44s` or `t=1m45s`, so the list cannot say whether the occlusion came before or after a given IPC message.
- Workaround: `--json` piped through a small script printing `start/1000` to 4 decimals. What the default output could have shown: seconds with ms precision (`t=104.588s`) whatever the profile length, or ms relative to the zoom start.

## Question: which IPC message types a process received at time X

- Command: `profiler-cli thread markers --search 'DOMEvent,IPCIn' --list --limit 0 --session <s>` on a content process thread.
- Expected: the message type (e.g. `PBrowser::Msg_UpdateEffects`) on each IPCIn/IPCOut row.
- Got: the rows' label is blank (`IPCIn` then nothing), so dozens of rows need `marker info` each.
- Workaround: `marker info m-A..m-B --json` and a script reading `fields[messageType]`. Also, in that JSON the records have no `handle` key, so the script could not print which handle each line was.

## Question: what the parent main thread was doing while an IPC message sat queued for 11 ms (review)

- Command: `profiler-cli zoom push 2.5195,2.5312 --session <s>` then `profiler-cli thread samples-top-down --include-idle --session <s>` on the parent main thread (task YnR9ldGOSf-Pevs7iQEiQA).
- Expected: either the stacks in that range, or a line saying the range holds no samples (CI samples are 10 ms apart).
- Got: a bare `Top-Down Call Tree:` header with nothing under it, which reads like a failed query. A wider zoom then showed only the native wait, and the answer (a sync `PCompositorBridge::Msg_Resume` inside the occlusion runnable) was only in `thread markers --list --min-duration 0.5`.
- Workaround: `thread markers --list` over the range. What the output could have shown: "0 samples in this range (sample interval 10 ms)", and a pointer to the long markers covering the range.
