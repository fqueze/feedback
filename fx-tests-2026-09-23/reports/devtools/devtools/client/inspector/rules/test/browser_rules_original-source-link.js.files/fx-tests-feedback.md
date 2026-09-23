## A failure confined to one push that sheriffs classified "fixed by commit" (browser_rules_original-source-link.js)

- Question: were these failures caused by a push that was later backed out?
- Commands: `fx-tests test devtools/client/inspector/rules/test/browser_rules_original-source-link.js --history --task-ids --limit 0`, `fx-tests intermittent --bug 1432176`
- Expected: that all failures come from one revision (autoland b606af2b1dd6), that the push had 3 runs of the same job (the original and 2 retriggers), all failing identically, and that 2 of them are classified "fixed by commit" by sheriffs.
- Got: one task (NPZ-lvP8TBu_YrCw7jYOMQ.0) and "1 sheriff annotation" on the intermittent bug; nothing about the two other failing runs of the same job on the same push or their classification. That reads as a rare intermittent, when it was a 3/3 regression from a backed-out patch.
- Workaround: `treeherder-cli b606af2b1dd6 --repo autoland --include-intermittent --json`, which lists the 3 failing dt6 jobs with `failure_classification_id` 2/2/4, then the autoland pushlog to find the backout.
