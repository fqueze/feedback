## review-browser_dbg-javascript-tracer-next-interaction.js

Question: which main-thread markers longer than 1 ms started inside a zoomed range (what ran in a gap)?

- Command: `profiler-cli zoom push 4.719,4.840 --session …; profiler-cli thread markers --min-duration 3 --list --limit 60 --session …`
- Expected: markers that ran in the range.
- Got: the first 60 lines were all `IPCOut`/`IPCIn` markers that started at 1.1 s and last 3.6 s (they overlap the range), so none of the runnables were shown.
- Workaround: `--search "-name:IPCOut,-name:IPCIn"` plus awk on the `t=` column to keep only markers that started in the range. A `--started-in-range` option (or listing overlap-only markers last) would have answered it directly, and so would `--sort duration`.
