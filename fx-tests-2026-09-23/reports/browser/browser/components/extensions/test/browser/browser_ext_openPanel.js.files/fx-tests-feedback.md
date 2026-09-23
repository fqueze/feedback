## Question: which job (chunk) did this failure run in?

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_openPanel.js --task-ids --limit 0`
- Expected: the same job label as `fx-tests task <id>` gives for that task.
- Got: `S-3r7nlNRDmGuq_jnkTYDg.0  test-macosx1500-aarch64/opt-mochitest-browser-chrome-1` and `AEmGqp1cRCSU-jdk5L6_oQ.0 ... -chrome-1`, while `fx-tests task S-3r7nlNRDmGuq_jnkTYDg` and `fx-tests task AEmGqp1cRCSU-jdk5L6_oQ` both say `test-macosx1500-aarch64/opt-mochitest-browser-chrome-9`.
- Workaround: trusted `fx-tests task`. Harmless here, but a wrong chunk number would send someone to the wrong manifest neighbours.
