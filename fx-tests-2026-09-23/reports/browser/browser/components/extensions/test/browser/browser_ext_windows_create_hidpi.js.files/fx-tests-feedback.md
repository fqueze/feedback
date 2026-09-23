## `fx-tests test <path> --bugs` says nothing when no bug names the test

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_windows_create_hidpi.js --bugs`
- Expected: a "Bugs" section, or "no bug names this test".
- Got: output identical to the command without `--bugs`, so it is unclear whether the lookup ran. 
- Workaround: Bugzilla REST `bug?summary=<test>` and `quicksearch=<test>`, both empty.
