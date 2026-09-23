## `fx-tests task` on a max-run-time job says nothing about where the time went

- Command: `fx-tests task Qr7Jysh9Qeyf_PWui4KL-w --profiles`
- Expected: the tests that ran, their durations, and the time between them (browser restarts), from the partial resource stream.
- Got: `task ... was killed for exceeding its maximum duration, so its profile is a partial stream rather than a finished document and this tool does not read that format.`
- Workaround: aggregated `public/test_info/wpt_instruments.txt` (per-test and `stop_runner` times) and the live log with scripts (`agg_instr.py`, `restart_phases.py`).
- Question: "how much of a timed-out job's time went to tests versus harness overhead (restarts, setup), compared with a passing sibling config". Here the answer was 61% in `stop_runner`.
