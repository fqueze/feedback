## `test --bugs` prints nothing when no annotated bug exists, and misses a bug naming the test

- Command: `fx-tests test devtools/client/inspector/rules/test/browser_rules_user-property-reset.js --bugs`
- Expected: a "Bugs" section, saying "none" if nothing was found; ideally also open bugs whose summary names the test file.
- Got: the normal output with no bug section at all, nothing on stderr, exit 0. So "no bug" and "the flag did nothing" look the same.
  A Bugzilla quicksearch for `user-property-reset` finds bug 1763787 ("Investigate leaks on windows debug for
  browser_rules_user-property-reset.js", NEW), which has no sheriff annotations and so is out of `--bugs`' scope.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?quicksearch=<test name>"`.
