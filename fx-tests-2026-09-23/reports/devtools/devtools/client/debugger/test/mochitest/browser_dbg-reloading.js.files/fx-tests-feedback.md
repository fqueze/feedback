## `fx-tests test <path> --bugs` prints nothing when no open bug matches

- Command: `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-reloading.js --bugs`
- Expected: a "Bugs" section, even if it says "none" (Bugzilla has three bugs naming the test, all resolved: 2026454, 2070920, 2071579).
- Got: the same output as without `--bugs`, so "no bug", "only resolved bugs" and "flag ignored" look identical.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=browser_dbg-reloading.js`.
