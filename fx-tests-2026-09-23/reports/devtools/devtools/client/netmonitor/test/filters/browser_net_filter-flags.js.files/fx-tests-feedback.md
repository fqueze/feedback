## Question: did the try push schedule the config that the test's exclusion covers?

- Command: `fx-tests try 2888bcab0070 --all-jobs --test devtools/client/netmonitor/test/filters/browser_net_filter-flags.js`
- Expected: next to the configs that ran the test, the push's configs that did not run it, with the reason: "http3 config not scheduled on this push", or "skipped by run-if !http3".
- Got: only the 7 configs that ran it. Nothing says whether the one config that matters (`*-http3`) was absent from the push or present but skipping the test.
- Workaround: read the Treeherder job list the parent had saved (`todo.files/try-jobs.json`) and check it for `http3` job types. Sibling reports (filter-03, filter-04) did the same.

## `fx-tests test <path> --bugs` prints nothing when no bug names the test

- Command: `fx-tests test devtools/client/netmonitor/test/filters/browser_net_filter-flags.js --bugs`
- Expected: a line such as "Bugs: none name this test".
- Got: the default summary with no Bugs section, which looks the same as the flag being ignored.
- Workaround: none needed. Read the silence as "no bug".
