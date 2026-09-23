## Empty call tree with no explanation on a narrow zoom

- Command: `profiler-cli zoom push 3.890,3.930 --session <s>` then `profiler-cli thread samples-top-down --session <s> --include-idle`
- Expected: a tree, or a line saying the range has 0 samples.
- Got: `Top-Down Call Tree:` followed by nothing. The profile samples every 10 ms but that main thread had only 6 samples in 150 ms, so 40 ms held none; `thread samples` on a wider range showed the count.
- Workaround: widened the range and used `thread samples` for the sample count.
