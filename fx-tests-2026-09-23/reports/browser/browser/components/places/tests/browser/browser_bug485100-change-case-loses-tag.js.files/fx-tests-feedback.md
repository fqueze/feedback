## `errors --message ... --limit 0` still truncates the tests list

- Command: `COLUMNS=250 fx-tests errors --message "No anchor node for bookmark" --limit 0`
- Expected: all 6 tests emitting the message.
- Got: 5 tests and `… 1 more tests`, same as without `--limit 0`.
- Workaround: none; the sixth test stays unknown.
