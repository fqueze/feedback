## `fx-tests errors --message ... --limit 0` does not expand the tests list

- Command: `COLUMNS=250 fx-tests errors --message "No anchor node for bookmark" --limit 0`
- Expected: all 6 tests emitting the message (the question: "which other tests hit the same product error?").
- Got: the list still ends with `… 1 more tests`, with or without `--limit 0`.
- Workaround: none tried; the sixth test stays unknown.

