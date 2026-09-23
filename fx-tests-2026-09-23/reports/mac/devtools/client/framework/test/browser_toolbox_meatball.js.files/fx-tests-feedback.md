## Question: which job ran this test and passed?

- Question: on a try push, which task is the one where the test ran and passed (to compare configs, and to read its resource-usage profile).
- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs --task-ids --json`
- Expected: the passing job's task ID next to `"outcomes": {"passed": 1, ...}`.
- Got: `outcomes.passed: 1` and `totalJobs: 4`, but `taskIds` and `profiles` list only the three failing tasks; the text output does not name the passing job either.
- Workaround: Treeherder `api/project/try/jobs/?push_id=...` filtered by job name, then `fx-tests task ItolE-VrSXemBV-ZfutAlQ --json` to confirm `browser_toolbox_meatball.js` PASS there.
