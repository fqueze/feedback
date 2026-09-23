## Question: does the test run and pass on the other standalone configs? (browser_tab_manager_groups.js)

- Command: `fx-tests test <path> --coverage`
- Expected: the rows for the `-standalone` configs, since the only failing config is a standalone one and the brief uses standalone as an order-dependency check.
- Got: the table is truncated to 10 rows sorted by run count, so no standalone row appeared (`… 41 more`).
- Workaround: `--coverage --limit 0 | rg standalone`.
- Could have shown: when the failing configs share a variant suffix (`-standalone`), list the sibling configs with that suffix first, or a `--config <substring>` filter on `--coverage`.
