## `test --bugs` prints nothing when it finds no open bug

- Command: `fx-tests test devtools/client/inspector/markup/test/events/browser_markup_view-original-source.js --bugs`
- Expected: a Bugs section, even if it says "none found" (and ideally closed ones naming the test).
- Got: the same output as without `--bugs`, no line about bugs at all, so I could not tell whether it searched.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=browser_markup_view-original-source`, which found three bugs naming the test, all RESOLVED INCOMPLETE (1552579, 1821842, 2002698).
