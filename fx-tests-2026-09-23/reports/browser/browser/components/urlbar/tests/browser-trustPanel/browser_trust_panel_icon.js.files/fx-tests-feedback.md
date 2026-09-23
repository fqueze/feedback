## `fx-tests test <path> --bugs` with no matching bug

- Command: `fx-tests test browser/components/urlbar/tests/browser-trustPanel/browser_trust_panel_icon.js --bugs`
- Expected: a line saying no bug names this test, e.g. `Bugs: none found`.
- Got: exactly the same output as without `--bugs`. It is unclear whether the search ran and found nothing or the flag was ignored. I checked Bugzilla by hand.
