# fx-tests feedback

## Question: how many times did this test run on the try push, and on which configs did it pass?

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs` (and without `--all-jobs`)
- Expected: the `here` column to count every run of the test on the push, or to say which configurations it covers.
- Got: `browser_smartblockembeds_trustpanel.js  2/4`, and the detail lists only the two failing configs. `fx-tests try <rev> --all-jobs --test <path>` shows the push ran it in 6 job runs: `debug-mochitest-browser-chrome-17` 2 passed, `opt-...-5` 1 passed 1 failed, `opt-...-no-nv-12` 1 passed 1 failed. So `here` seems to count only the configurations where it failed, which makes "2 of 4" read as the whole push when it is 2 of 6 (and hides that debug never failed).
- Workaround: `--all-jobs --test <path>`, which the brief does not mention; one extra slow run of `--all-jobs`.

## Question: why is a failing test "1 failing execution of 1" when the job retried its manifest?

- Command: `fx-tests task QEv773mkQ32jh0IVHcRsug --profiles --messages`
- Got: `browser_smartblockembeds_trustpanel.js  TIMEOUT — 1 failing execution of 1`, next to `browser_smartblockembeds.js  TIMEOUT — 2 failing executions of 2` from the same manifest. It does not say that the retry did not rerun this test at all, or why, so "the retry passed" and "the retry never ran it" have to be told apart from the resource-usage profile.
- Could have shown: "not rerun by the harness retry" on the row.
