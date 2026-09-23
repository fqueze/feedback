## Finding a plain mochitest's log
- Command: `profiler-cli thread markers --category Test --search test_autoplay_policy_key_blacklist --list --limit 0` on the parent GeckoMain (t-0), as the brief suggests.
- Expected: the test's TEST-PASS / TEST-UNEXPECTED-FAIL markers.
- Got: 0 markers, with no hint that TestStatus markers exist on another thread. For plain mochitests they are on the content process main thread (`http://mochi.test`).
- Then `profile markers --search TEST-` (all threads) was flooded by `Preference Read` markers whose pref names contain "test-", so it did not point to the right thread either.
- Workaround: `thread markers` without a search on the content thread, reading its `By Category` counts, then `--category Test`.
- Could show: when a search finds nothing on the selected thread, list which threads have matches (or have Test-category markers).

## Search term containing " - "
- Command: `thread markers --search "MozAfterPaint,focus - browser,keydown" --list` (retry profile, t-0).
- Expected: the `DOMEvent  focus - browser@...` rows as well.
- Got: none of them, although `--search focus` returns them. The displayed label "focus - browser@..." does not seem to be searchable as one string.
- Workaround: search `focus` and filter the output.

## `--session` before the subcommand (review)
- Command: `profiler-cli --session <id> zoom push 10.44,10.56`
- Expected: the option accepted in any position, as for most CLIs.
- Got: `error: unknown option '--session' (Did you mean --version?)`. It only works after the subcommand (`profiler-cli zoom push ... --session <id>`).
- Workaround: put `--session` last.
- Could show: accept it globally, or say "put --session after the subcommand" in the error.

## Which window a DOMEvent was dispatched in (review)
- Question: "did this keydown / focus / MozAfterPaint happen in the popup's chrome window or the opener's?"
- Command: `thread markers --search DOMEvent --list --limit 0` on the parent main thread, with several browser windows open.
- Got: rows like `blur - document`, `MozAfterPaint - window`, `keydown - html:body@...`, with nothing telling the windows apart. The answer is the `innerWindowID` in `marker info --json` `rawFields`, one marker at a time.
- Could show: the innerWindowID (or window URL) in the list row, or a `--search innerWindowID:122` filter.
