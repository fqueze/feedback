## Question: does the test still fail anywhere after it stopped failing on trunk?

- Commands: `fx-tests test <path> --history` (0 failures since 2026-09-03) and `fx-tests intermittent --bug 1915308` ("no sheriff annotations for bug 1915308 on trunk between 2026-09-15 and 2026-09-21").
- Expected: some hint that the failure continues on release branches.
- Got: both read as "fixed"; only `fx-tests intermittent --bug 1915308 --since 21 --tree all` showed 22 mozilla-esr153 + 3 mozilla-beta annotations, 7 on esr153 in the last 7 days (the fix was not uplifted).
- Workaround: `--tree all`. The "no annotations on trunk" message does suggest `--tree all`, which is how I found it; `fx-tests test`'s verdict could mention non-trunk annotations when the bug is known, or `intermittent --bug` could default to all trees since a bug id is already narrow.
