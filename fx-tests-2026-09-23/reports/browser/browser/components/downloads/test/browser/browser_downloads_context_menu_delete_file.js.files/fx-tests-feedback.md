## `fx-tests test <path> --bugs` shows no bug section at all

- Command: `fx-tests test browser/components/downloads/test/browser/browser_downloads_context_menu_delete_file.js --bugs`
- Expected: a list of the bugs whose summary names the test, or an explicit "no bugs found" line. Bugzilla has 7 bugs naming this test (1749734, 1751076, 1751507, 1759944, 1761491, 1775783, 1800947), all closed.
- Got: the same output as without `--bugs`, with no bug section and no message, on stdout and stderr.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=<test file name>'`.
