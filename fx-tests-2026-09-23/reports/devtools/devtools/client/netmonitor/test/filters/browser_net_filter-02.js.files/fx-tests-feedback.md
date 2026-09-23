## Question: did the try push schedule any job of a given variant (here http3)?

- Command: `fx-tests try 2888bcab0070 --all-jobs --test devtools/client/netmonitor/test/filters/browser_net_filter-02.js`
- Expected: to tell "variant not scheduled" apart from "variant ran and skipped the test", e.g. a line listing configs of the push on which the test was not run, or the push's scheduled configs.
- Got: only the 7 configs that ran the test. A missing http3 row looks the same whether no http3 job ran or one ran and `run-if` excluded the test.
- Workaround: a Python script over the Treeherder job list (`todo.files/try-jobs.json`), grepping job names for `http3`. Nothing matched.
- Also: `fx-tests test <path>` shows "188x SKIP http3" as an Issue for a condition that has been a `run-if` since 2026-09-17 (skip records stop that day). Only `--history` shows that the skip is no longer in force. A "last seen" date on the SKIP issue line would answer that directly.
