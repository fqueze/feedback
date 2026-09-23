## Which test emitted this C++ warning (resource-usage profile)
- Command: `profiler-cli thread markers --session <s> --search "name:C++ warning" --list --limit 0`
- Expected: each row names the test that emitted the warning. In a resource-usage profile, 20 xpcshell processes run in parallel, so without that the warning cannot be tied to anything.
- Got: message and file:line only. The payload's `Test` field only shows through `marker info m-N`, one call per marker, or through `--json`.
- Workaround: `marker info m-1 m-2 ...`. For TestStatus/cppDebug markers, the row could show the `Test` field the way it shows the test name after the em dash for INFO markers.
