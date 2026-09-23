## `fx-tests task` shows the "force-killed" message for some TIMEOUTs and not others whose profile markers are identical

- Command: `COLUMNS=250 fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles --limit 0`
- Expected: the same message lines for tests whose markers are the same. `test_IPPL10nHelper.js`, `test_IPPOnboardingMessageHelper.js` and `test_HeapSnapshot_takeCensus_12.js` each have a `test` TIMEOUT marker "Test timed out" plus an ERROR marker "`<id>` | Timed out and was force-killed by the harness; …" at t≈1m37–38s.
- Got: `test_IPPOnboardingMessageHelper.js` lists both lines; `test_IPPL10nHelper.js` and `test_HeapSnapshot_takeCensus_12.js` list only "Test timed out".
- Workaround: read the markers in the profile. The difference made me check whether this test's failure was of another kind; it was not.
