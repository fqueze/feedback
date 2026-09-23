## Issues list does not group messages that differ only by an embedded number

- Command: `fx-tests test browser/components/search/test/browser/browser_search_lastused_pref.js`
- Question: "which failure modes does this test have, with their counts?"
- Expected: one Issues row `498x FAIL The urlbar search was recorded - N > N`.
- Got: 498 rows of `1x`, each with a different timestamp (`1788222900 > 1788222900`, ...), so the list reads as 498 distinct failure modes and `--limit 0` is needed to see that there is only one.
- Workaround: `--limit 0 | sed -E 's/[0-9]{6,}/N/g' | sort | uniq -c`.
- Could show: normalize long numbers (timestamps, ids) before grouping, as Treeherder does.

