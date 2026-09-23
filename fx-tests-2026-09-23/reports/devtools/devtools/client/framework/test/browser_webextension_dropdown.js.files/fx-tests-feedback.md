## `fx-tests try --test` job counts disagree between default and `--all-jobs`

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/framework/test/browser_webextension_dropdown.js` then the same with `--all-jobs`.
- Expected: the same `jobs` count per config in both views.
- Got: the default view shows 4 jobs for linux opt-5, debug-6 and a11y-checks-5, while `--all-jobs` shows 3 for each. The extra one seems to be a task run with no resource-usage profile (e6fPXv0dTUWlpLrNpuNcMw.0), but neither view says so.
- Workaround: ran `fx-tests task <id>.<run>` on each run to find the one with no profile.
- What would help: label runs that have no profile ("1 run without a profile") instead of counting them as jobs that ran the test.
