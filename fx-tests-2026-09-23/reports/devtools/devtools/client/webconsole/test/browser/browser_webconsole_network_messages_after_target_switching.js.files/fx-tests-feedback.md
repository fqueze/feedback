## `--bugs` with no bug found prints nothing (browser_webconsole_network_messages_after_target_switching.js)

- Command: `fx-tests test devtools/client/webconsole/test/browser/browser_webconsole_network_messages_after_target_switching.js --bugs`
- Expected: a "Bugs: none naming this test" line.
- Got: the same output as without `--bugs`, so "no bug" is indistinguishable from "the flag was ignored". Workaround: Bugzilla REST quicksearch.
