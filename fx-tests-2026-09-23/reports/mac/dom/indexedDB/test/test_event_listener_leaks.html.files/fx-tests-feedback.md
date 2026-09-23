## Question: "which other tests fail with this same message?"
- Command: `fx-tests failures --harness mochitest --limit 0 --full-messages | grep "content window should be garbage collected"`
- Expected: a way to filter `failures` by message substring (as `errors --message` does), and the test path on each row.
- Got: the full tree-wide list (thousands of rows) had to be grepped. Rows show the message and a test count but not which test, so "IDB document.open()" could only be mapped to its test through the API name in the message.
- What the default output could show: `fx-tests failures --message <substring>`, listing the test paths behind each matching message.

## `intermittent --bug` default window
- Command: `fx-tests intermittent --bug 1777041`
- Got: exit 2, "no sheriff annotations ... on trunk between 2026-09-16 and 2026-09-22". `--since 21 --tree all` found 7, 2 of them on mozilla-esr140. Fine, and the hint said what to do. Noting it only because the brief's command gives nothing for low-frequency bugs, so the report's "annotated in the last 7 days" is 0 while the 21-day count is not.
