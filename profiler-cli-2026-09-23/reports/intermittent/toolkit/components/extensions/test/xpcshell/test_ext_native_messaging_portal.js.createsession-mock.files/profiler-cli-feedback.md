## Question: when were the samples in this window, and what was each one's leaf?

- Command: a shell loop of `profiler-cli zoom push <s>,<s+0.01>` + `thread samples-bottom-up --include-idle --limit 3` + `zoom pop`, over 16 windows of 10 ms (2.855–3.015 s), to find which sample had the lone `libglib-2.0.so.0!g_variant_new_from_bytes` leaf and when the main thread was in `PollWrapper`.
- Expected: one command listing the samples in the current zoom, with their time, category and leaf frame (or the stack) — the equivalent of the profiler's sample tooltip, for sparse (10 ms) CI profiles where a window holds only a few samples.
- Got: only aggregated trees; timing is lost. 48 commands for one answer.
- Workaround: bisecting with zoom windows.
