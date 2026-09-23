## `--durations` hides the failing config's row

- Command: `fx-tests test devtools/server/tests/xpcshell/test_connection_closes_all_pools.js --durations`
- Question: how long does a passing run take on the one config that fails (test-windows11-64-25h2/opt-xpcshell-msix)?
- Expected: that config's row, since it is the only failing config and the verdict names it.
- Got: the 10 slowest configs (ccov, tsan, debug), `… 29 more (--limit 0 for all)`; the msix rows were not among them.
- Workaround: `--durations --limit 0 | grep msix`. Always showing the failing configs' rows (or accepting `--config`) would answer it directly.
