## `task --messages` lists expected (todo_is) failures as the test's failure messages

- Question: which assertions of this test failed unexpectedly in this job.
- Command: `fx-tests task KEpw-KUiTL23-qL1s0yu9A --messages` (same for `SK5Ajw3nTU2m2P1Z-lflKQ`, `JvWf3kbrQVSItB2FfPokwA`).
- Expected: the two TEST-UNEXPECTED-FAIL messages (`IME should be enabled when the <html> element ...`, `The <html> should be observed by IMEContentObserver ...`), with TEST-KNOWN-FAIL ones left out or marked as expected.
- Got: four messages at the same level, sorted alphabetically, so the first listed is `IME should be enabled when the <body> element whose contenteditable is set to true and it has focus - got +0, expected 1`, which is a `todo_is` in the test's third subtest and fails as expected in every run. `--json` has only `message` and `count`, no status/expected field to tell them apart.
- Workaround: read the test source to see which messages come from `todo_is`. The resource-usage profile's `FAIL` TestStatus markers carry the same ambiguity (m-20 for the todo has no stack, the real ones do), which is probably where it comes from.
