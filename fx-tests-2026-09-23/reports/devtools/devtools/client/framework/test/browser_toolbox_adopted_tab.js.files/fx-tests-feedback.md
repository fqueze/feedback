## `fx-tests test <path> --bugs` prints no bug section

- Command: `fx-tests test devtools/client/framework/test/browser_toolbox_adopted_tab.js --bugs`
- Expected: bug 2049357 ("Intermittent devtools/client/framework/test/browser_toolbox_adopted_tab.js | adoptTabToExistingWindow - Test timed out", RESOLVED FIXED), or an explicit "no bug names this test".
- Got: the same output as without `--bugs`, with no Bugs section at all — indistinguishable from the flag being ignored.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=browser_toolbox_adopted_tab`.
