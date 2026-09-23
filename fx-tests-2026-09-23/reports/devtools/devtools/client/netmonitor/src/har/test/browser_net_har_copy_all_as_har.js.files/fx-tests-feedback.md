## `test --bugs` prints no bug list

- Command: `fx-tests test devtools/client/netmonitor/src/har/test/browser_net_har_copy_all_as_har.js --bugs`
- Expected: the bugs whose summary names the test. Bugzilla quicksearch finds 2068027, and bug 2067787 has the test path in its summary.
- Got: the normal `test` output. No bug section, nothing on stderr saying "no bugs found".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?quicksearch=<test file name>`.
