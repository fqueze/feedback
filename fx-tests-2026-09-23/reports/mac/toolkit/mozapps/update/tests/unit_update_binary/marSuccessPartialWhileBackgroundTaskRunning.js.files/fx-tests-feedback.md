## Question: is there a bug naming this test?

- Command: `fx-tests test toolkit/mozapps/update/tests/unit_update_binary/marSuccessPartialWhileBackgroundTaskRunning.js --bugs`
- Expected: a "Bugs" section listing matching bugs, or an explicit "no sheriff-annotated bug names this test" line
- Got: the same output as without `--bugs`, with no bug section and no line saying none was found, on stdout or stderr
- Workaround: searched Bugzilla REST by summary substring (`/rest/bug?summary=WhileBackgroundTaskRunning`). That found closed bugs (e.g. bug 2068839, enterprise-main) and no open one
- What the output could have shown: an explicit empty result, so "none" and "the flag did nothing" look different
