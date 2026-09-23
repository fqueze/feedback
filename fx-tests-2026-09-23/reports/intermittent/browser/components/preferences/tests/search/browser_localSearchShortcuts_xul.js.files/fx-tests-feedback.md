## Verdict says "intermittent" for a perma-fail that started mid-window

- Command: `fx-tests test browser/components/preferences/tests/search/browser_localSearchShortcuts_xul.js`
- Expected: a verdict saying it fails every run on the `-shippable` configs since 2026-09-11 (a step change), since `--history` shows 0 failures before 09-11 and the "recent" column shows 100.0% on 8 configs.
- Got: `Verdict: intermittent ... worst is ... at 66.7% (72/108)`, with the rate computed over the whole 21-day window, and every failing config listed at ~62%. Only the "recent 100.0%" column and a separate `--history` call reveal that it is perma since a date.
- Workaround: read `--history` and the `recent` column. The verdict could use the recent rate, or name the step date when daily counts go from 0 to perma.
