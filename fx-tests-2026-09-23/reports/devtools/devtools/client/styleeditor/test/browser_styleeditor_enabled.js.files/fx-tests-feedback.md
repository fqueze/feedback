## `--bugs` says nothing when no bug is found

- Command: `fx-tests test devtools/client/styleeditor/test/browser_styleeditor_enabled.js --bugs`
- Expected: a "Bugs naming this test" section, reading "none" when there are none.
- Got: the regular output with no bugs section at all, which looks the same as the flag being ignored or the lookup failing silently. For a test that has one (browser_aboutdebugging_process_main_local.js), the section does appear.
- Workaround: ran it on a test known to have a bug to confirm the flag works, then searched Bugzilla directly.
