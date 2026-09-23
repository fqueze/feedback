## `--bugs` with no bug found prints nothing about bugs

- Command: `fx-tests test toolkit/mozapps/update/tests/unit_update_binary/marStageSuccessPartial.js --bugs`
- Expected: a "Bugs" section, saying "none found" when no bug names the test.
- Got: the same output as without `--bugs`, with no bug section at all, so "none found" and "the flag did nothing" look the same.
- Workaround: took the absence as "none"; no way to confirm from the output.
