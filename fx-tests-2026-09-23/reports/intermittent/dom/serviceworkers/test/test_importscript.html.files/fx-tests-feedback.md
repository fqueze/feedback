## Question: how many runs on one platform (android) in total, across its configs?

- Command: `fx-tests test dom/serviceworkers/test/test_importscript.html --coverage`
- Expected: a per-config table of runs/pass/fail (the coverage table), or a per-platform run total, so that "5 failures in N Android runs" can be read directly.
- Got: the text output only printed `android  21/28 ran — 7 scheduled but skipped`; the per-config coverage rows with run counts are only in `--json` (`.coverage.configs[]`).
- Workaround: summed `.coverage.configs[].runCount` for android job names with a python script.
