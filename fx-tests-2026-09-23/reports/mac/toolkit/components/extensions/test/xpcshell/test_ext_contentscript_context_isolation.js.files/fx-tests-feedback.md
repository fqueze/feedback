## Question: "which bugs name this test?" — `--bugs` prints nothing when there are none

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_contentscript_context_isolation.js --bugs`
- Expected: a "Bugs" section, even if it only says "no sheriff-annotated bug in the window" (and ideally the open/closed bugs whose summary names the test).
- Got: the same output as without `--bugs`; no section at all, so I could not tell "none" from "the flag did nothing". Needed `--json` to see `"annotatedBugs": []`, then a Bugzilla REST search to find 6 closed bugs naming the test (e.g. 1659984 for this very timeout).
- Could have shown: an explicit empty line, plus the bugs whose summary names the test with their status.
