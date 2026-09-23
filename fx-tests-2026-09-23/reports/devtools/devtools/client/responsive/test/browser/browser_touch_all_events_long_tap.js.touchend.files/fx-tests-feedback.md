## `--bugs` prints nothing at all when it finds no annotated bug

- Command: `fx-tests test devtools/client/responsive/test/browser/browser_touch_all_events_long_tap.js --bugs`
- Expected: a bugs section, even if it says "none", or the RESOLVED bug 2012028 whose summary names the test ("Intermittent devtools/client/responsive/test/browser/browser_touch_all_events_long_tap.js | single tracking bug").
- Got: exactly the output without `--bugs`; no line about bugs, so "no bug" and "the flag did nothing" look the same.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=browser_touch_all_events_long_tap`.

## Question: is every failing run of this test the same failure, and did each retry pass?

- Command: `fx-tests test <path>` (Issues: "first failure per run"), then `fx-tests task <id> --profiles` and `fx-tests task <id> --messages` for each of the 25 failing tasks.
- Expected: per failure mode, how many failing runs contain it at all (not only as the first message), and how many of those jobs' harness retries passed.
- Got: issue 5 (`handleEvent() was unable to perform a11y checks on hidden node`, 2x) reads like a separate failure mode, but both of its runs also have the touchend timeout of issue 4, so the touchend failure is in 25/25 runs. Retry outcome only per task.
- Workaround: 25 `fx-tests task` calls and a grep over them.
- What would have answered it: a "runs containing" count next to each issue and a "retry passed n/m" column in `fx-tests test`.
