## `--bugs` prints nothing at all, not even "no bugs found", for a moved test

- Command: `fx-tests test browser/components/urlbar/tests/browser-trustPanel/browser_trust_panel.js --bugs`
- Expected: the sheriff-annotated bugs naming this test — bug 2012567 ("Intermittent browser/components/urlbar/tests/browser/browser_trust_panel.js | single tracking bug", 16 of its 28 annotated jobs in the last 21 days name the new `browser-trustPanel/` path, per `fx-tests intermittent --bug 2012567 --since 21 --tree all`) — or at least a line saying none was found.
- Got: exactly the default output, with no bugs section and no message on stdout or stderr, so "no bugs" and "the lookup silently did nothing" look the same.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=browser_trust_panel.js'`, then `fx-tests intermittent --bug 2012567 --since 21 --tree all`. The bug is filed under the test's path before its 2026-07-22 move into `browser-trustPanel/`; matching on the file name would have found it.
