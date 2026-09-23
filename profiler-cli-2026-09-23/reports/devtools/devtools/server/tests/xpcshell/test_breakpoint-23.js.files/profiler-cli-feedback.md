## Question: which tests *started* after a given time, and with what status

- Question: in a resource-usage profile, did any test that started after the MSIX package broke (first `Error:-2147009284` marker) PASS, and how many TIMEOUTs started before it?
- Command tried: `profiler-cli zoom push 131.68,<end>` then `thread markers --search name:test --group-by field:status`.
- Expected: counts of `test` markers starting in the range, by status.
- Got: the zoom keeps every interval marker overlapping the range, so tests that started long before the break (the 1m31s `parallel` group, earlier TIMEOUTs) are counted too, and the grouping cannot separate them.
- Workaround: `thread markers --search name:test --list --limit 0 --json` and a script filtering on `start`. A `--starts-in <range>` (or a zoom mode that keeps only markers starting inside) would have answered it.
