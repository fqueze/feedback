## `--bugs` prints nothing when the only bug naming the test is closed

- Command: `fx-tests test devtools/client/webconsole/test/browser/browser_webconsole_network_messages_expand_before_updates.js --bugs`
- Expected: a Bugs section, listing bug 1767727 ("Intermittent …expand_before_updates.js | single tracking bug", RESOLVED INCOMPLETE 2025-11-17) as closed, or at least saying "no bugs found".
- Got: exactly the output without `--bugs`, with no Bugs section and no "none" line, so "no bug" and "the lookup did nothing" look the same.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=<test name>`.
