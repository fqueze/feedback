## Question: how many of this test's failures share one failure mode?

- Command: `fx-tests test browser/components/search/test/browser/browser_search_lastused_pref.js --harness mochitest`
- Expected: one Issues row, `478x FAIL The urlbar search was recorded - N > N`, since all failures are the same assertion with a timestamp in it.
- Got: 478 rows of `1x`, one per distinct timestamp (`... - 1788154863 > 1788154863`, `... - 1788157763 > 1788157763`, ...), truncated to 10 with "… 468 more".
- Workaround: `--limit 0`, then `sed -E 's/[0-9]{6,}/N/g' | sort | uniq -c` over the text output.
- Could have shown: Issues grouped with long numbers (and hex addresses, pids) normalized, as the `failures` view presumably needs anyway.

## `--bugs` prints nothing when there is no bug

- Command: `fx-tests test <path> --harness mochitest --bugs`
- Expected: a line such as "No sheriff-annotated bugs name this test."
- Got: the same output as without `--bugs`, with no bugs section at all, so "no bug" and "the flag did nothing" look the same.
- Workaround: `--bugs --json`, which has `"annotatedBugs": []`.
