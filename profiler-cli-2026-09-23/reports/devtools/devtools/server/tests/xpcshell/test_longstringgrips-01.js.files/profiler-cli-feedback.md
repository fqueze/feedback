## Question: exact time of markers after the first minute

- Command: `profiler-cli thread markers --session <s> --search test_longstringgrips-01 --list --limit 0`
- Expected: millisecond timestamps for every row, as for rows before 60 s (`t=46.593s`).
- Got: rows after 60 s print `t=1m32s`, so the Begin/ERROR/End log-replay markers and the TIMEOUT end could not be ordered or compared to ms precision.
- Workaround: `marker info <handles> --json` and read `start`.
