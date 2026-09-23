## Question: which tests are behind this crash signature?

- Command: `fx-tests crashes --signature pref_SetPref --full-messages`
- Expected: the tests behind the signature (the row says "5 tests"), with their counts.
- Got: only the count row (`1,122  5  0  @ pref_SetPref`); the 5 test paths are not printed.
- Workaround: `fx-tests crashes --signature pref_SetPref --json` has `rows[].tests[]` with paths and counts.
- What the default output could show: when filtered to one signature (or with few rows), list the tests under the row.

