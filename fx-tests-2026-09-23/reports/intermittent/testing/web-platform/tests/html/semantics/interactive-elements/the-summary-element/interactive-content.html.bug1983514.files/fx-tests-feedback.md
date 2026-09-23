## `fx-tests test` has no web-platform-tests data

- Command: `fx-tests test testing/web-platform/tests/html/semantics/interactive-elements/the-summary-element/interactive-content.html`
- Expected: failure rates per config for a WPT test (the bug is a WPT intermittent: `fx-tests intermittent --bug 1983514` found it fine).
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...".
- Workaround: listed the `web-platform-tests-backlog-2` jobs via the Treeherder jobs API, downloaded each job's `wpt_errorsummary.log`, and read the `group_result` for `/html/semantics/interactive-elements` (OK or ERROR) to get per-config rates. That was about 100 downloads and a script. `fx-tests task <id>` did work on a WPT job and showed the test's status and message.
- Question the default output could have answered: "how often does this WPT test have unexpected results, per config, and at which subtest".
