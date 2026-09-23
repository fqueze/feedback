## `fx-tests test` Issues does not group messages differing only in GUIDs/timestamps

- Command: `fx-tests test toolkit/components/places/tests/unit/test_keywords.js`
- Expected: one issue row "24x FAIL [test_deleteKeywordMultipleBookmarks : 106] ... deepEqual ..." (the messages differ only in random bookmark GUIDs and the `lastModified` ISO timestamp).
- Got: 24 rows of "1x", each ~4 lines of JSON, truncated at 10 with "… 15 more"; the one real failure mode reads as 24 distinct ones, and the counts per mode have to be worked out by hand.
- Workaround: `--limit 0` and eyeballing that every row is "↑ same as 2".
- Suggestion: normalise GUID-like 12-char tokens and ISO timestamps (and numbers generally) before grouping.

## Question: "do all occurrences of this failure mode share the same discriminating values?"

- Command: `COLUMNS=1000 fx-tests test toolkit/components/places/tests/unit/test_keywords.js --limit 0 --full-messages`, then a Python script parsing each "actual deepEqual expected" pair.
- Question: across the 24 FAIL rows, are the two `lastModified` values always equal, and is the id order always (7,8) vs expected (8,7)? That is the whole diagnosis for an ordering flake.
- What could have answered it: with the GUID/timestamp normalisation suggested above, one grouped row plus the list of the values that varied (or "varies: guid, lastModified; constant: ids, order") would have shown that the variable parts are only the random ones.

