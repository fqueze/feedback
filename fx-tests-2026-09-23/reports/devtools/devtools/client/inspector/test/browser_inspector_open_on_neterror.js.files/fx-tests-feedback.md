## `fx-tests test <path> --bugs` prints no bug section at all

- Command: `fx-tests test devtools/client/inspector/test/browser_inspector_open_on_neterror.js --bugs`
- Expected: a "Bugs" section, or an explicit "no open bug names this test".
- Got: the same output as without `--bugs`, nothing on stderr either. Bugzilla has three closed bugs naming the test (1326553, 1691608, 1726259), so it is unclear whether the flag searched and found no open bug, or did nothing.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=<test name>`.
