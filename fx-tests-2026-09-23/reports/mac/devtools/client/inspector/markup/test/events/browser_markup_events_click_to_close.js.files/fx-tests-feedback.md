## `fx-tests test <path> --bugs` prints nothing about bugs when there are none

- Command: `fx-tests test devtools/client/inspector/markup/test/events/browser_markup_events_click_to_close.js --bugs`
- Expected: a "Bugs" section, saying "none" when no open bug names the test.
- Got: the same output as without `--bugs`, with no bug section at all, so it is unclear whether the search ran. A Bugzilla summary search found three closed bugs naming the test (1449625, 1575203, 1828030).
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?short_desc=click_to_close&short_desc_type=substring`.
