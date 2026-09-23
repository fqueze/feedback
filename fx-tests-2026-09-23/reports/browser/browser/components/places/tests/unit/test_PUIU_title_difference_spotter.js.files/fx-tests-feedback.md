## `--bugs` with no annotated bugs prints nothing

- Command: `fx-tests test browser/components/places/tests/unit/test_PUIU_title_difference_spotter.js --bugs`
- Expected: a line such as "Annotated bugs: none" so the query visibly ran.
- Got: output identical to the run without `--bugs`; no mention of bugs at all. Had to use `--json` to see `annotatedBugs: []`.
- Workaround: `--bugs --json | jq .annotatedBugs`.
