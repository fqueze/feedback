## Question: what was the parent main thread doing while a setTimeout(0) waited 56 ms

- Command: `profiler-cli zoom push 13.891,13.946 --session browser-keyboard-2` then `profiler-cli thread samples-top-down --session browser-keyboard-2 --include-idle --limit 40` (CI profile of N_AtlST6RA-iAtbf8OFoDg, parent GeckoMain t-0).
- Expected: the call tree of the samples in the range, or a line saying the range holds no samples (and how many the thread has overall).
- Got: the header and `Top-Down Call Tree:` followed by nothing, the same whether or not `--include-idle` is passed. Widening to 13.80,14.00 with `thread samples` showed 3 samples, all "Native event loop idle", while `thread info` for the same zoom reported "90% for 69.1ms: (13.887s - 13.963s)" CPU activity — so the samples and the CPU line disagree and nothing says why (the thread has only 472 samples over 22 s).
- Workaround: none; relied on markers (setTimeout callback, DOMEvent) for the ordering.

## Question (review): which markers in this range have a stack through function X

- Command: `profiler-cli zoom push 13.947,13.952 --session browser-review-browser_keyboard_tests.js-2` then `profiler-cli thread markers --session ... --list --limit 0 --search SetNeedStyleFlush`, to find which `SetNeedStyleFlush` came from `head.js!cleanup`.
- Expected: a way to filter markers by a frame in their captured stack (e.g. `--stack-search head.js!cleanup`), like `--includes-function` does for samples.
- Got: `--search` only matches name and payload, so it listed 8 identical rows with no stack info.
- Workaround: a shell loop running `marker stack` and `marker info --json` on each handle, grepping for the frame.
