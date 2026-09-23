## Question: which IPC messages arrived on this thread, in order, in this window?

- Command: `profiler-cli thread markers --search PDocAccessible --list --limit 0 --session <s>` (also `--category IPC --list`)
- Expected: each IPCIn/IPCOut row to show its message type (e.g. `PDocAccessible::Msg_MutationEvents`) and sender, as the Runnable rows do in their label.
- Got: rows read only `IPCIn  t=27.082s  963.00μs` with no message type; the type is only in `marker info` or `--json` fields (`messageType`, `sendThreadName`). I had to script over `--list --json` to get a readable chronology.
- Could have shown: `IPCIn PDocAccessible::Msg_MutationEvents ← Web Content (7/7)` in the list's label column.

## `--list --json` omits `duration` on some flat markers

- Command: `profiler-cli thread markers --search PDocAccessible --list --limit 0 --json` piped to a script reading `m['duration']`
- Expected: `duration` present (null for instants) on every flatMarkers entry.
- Got: KeyError on some entries (instant markers such as `TimingDistribution::accumulate`), so the key is sometimes absent rather than null.
- Workaround: `m.get('duration')`.
