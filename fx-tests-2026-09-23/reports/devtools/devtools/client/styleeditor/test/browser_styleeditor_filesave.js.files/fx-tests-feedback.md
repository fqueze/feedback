## `fx-tests try` files a perma-fail under KNOWN INTERMITTENTS with a 0.0% central rate

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids`
- Expected: `browser_styleeditor_filesave.js` in PERMA-FAILS. It failed every run on `test-linux2404-64/opt-mochitest-devtools-chrome-a11y-checks-5` (3 of 3 jobs, retry failing too, per `--all-jobs --test`), and the detail block itself says "central 0.0% ... This exact failure was never seen in history — it looks new."
- Got: listed under "KNOWN INTERMITTENTS (14) — also fail on central; likely not yours." as `6  ...filesave.js  6/7  0.0%  0.0%`. The "also fail on central" heading contradicts the 0.0% on its own row. The "6/7" also does not match the 3 failed jobs x 2 executions of `--test` (where does the 7th run come from?).
- Workaround: `fx-tests try <rev> --all-jobs --test <path>` for the per-config truth.
