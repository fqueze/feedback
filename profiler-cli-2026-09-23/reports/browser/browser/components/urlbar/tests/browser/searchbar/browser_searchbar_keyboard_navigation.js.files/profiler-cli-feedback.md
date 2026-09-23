## How many samples are behind a percentage in a zoomed range?

- Command: `profiler-cli zoom push 46.053,46.066 --session <s>` then `profiler-cli thread samples-top-down --session <s> --include-idle` (and `thread samples --include-idle --json`).
- Question: is "100%" in `profiler_capture_backtrace` one sample or fifty? In a 13 ms window at CI's 10 ms interval it is surely one or two, and that decides whether the finding is evidence or noise.
- Expected: the sample count in the header (e.g. "2 samples in view"), or counts next to percentages in the tree.
- Got: percentages only; the `--json` top-level fields have no count either.
- Workaround: none; reported it as "the one sample" from the interval arithmetic.
- Follow-up: `thread samples-top-down --json` does carry `totalSamples: 1` per node; only the text output (and `thread samples --json` top level) omit it. The text tree printing "(1 sample)" at the root would have answered it without a script.
