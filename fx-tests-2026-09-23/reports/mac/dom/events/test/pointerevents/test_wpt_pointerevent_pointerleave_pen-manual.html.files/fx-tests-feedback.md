## `fx-tests test <path> --bugs` prints nothing when no bug names the test
- Command: `fx-tests test dom/events/test/pointerevents/test_wpt_pointerevent_pointerleave_pen-manual.html --bugs`
- Expected: a `Bugs` section, saying "none" when there are none.
- Got: the usual summary with no bug section at all, so "no bug" looks the same as "the flag did nothing".
- Workaround: searched Bugzilla directly (`rest/bug?short_desc=...`).
