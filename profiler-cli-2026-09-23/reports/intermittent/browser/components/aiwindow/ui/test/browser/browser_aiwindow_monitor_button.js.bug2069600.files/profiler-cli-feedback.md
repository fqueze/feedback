## Question: which tests ran in this browser session, and what was each one's status?

- Command: `profiler-cli thread markers --category Test --search "PASS —,FAIL —" --list --limit 0` on a per-test profile's parent main thread.
- Expected: one row per `test` marker. The rows display as `PASS — <path>`.
- Got: 0 markers. `--search name:test` matches every INFO/task marker whose text contains "test" (2035 rows).
- Workaround: `--search "browser/components/aiwindow" --list --limit 0 | rg ' test  '`.
- Could show: a way to match only the `test` marker type (e.g. `type:` or an exact-name match), or searching on the text as displayed.
