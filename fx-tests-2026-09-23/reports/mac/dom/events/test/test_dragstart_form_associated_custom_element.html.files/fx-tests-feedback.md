## `fx-tests test <path> --bugs` says nothing when no bug names the test

- Command: `fx-tests test dom/events/test/test_dragstart_form_associated_custom_element.html --bugs`
- Expected: an explicit line such as "Bugs: none name this test".
- Got: the same output as without `--bugs`, with no bug section, so "no bug" and "flag ignored / lookup failed" look identical.
- Workaround: queried Bugzilla REST directly (`/rest/bug?quicksearch=<test name>`), which returned no bugs.
