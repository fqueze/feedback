## `fx-tests try` cannot be asked about one test

- Question: "did `dom/events/test/browser_shortcutkey_ctrlf5_prevent_default.js` fail on try push `9db28f6db8bf`, and in which tasks?"
- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids`
- Expected: a way to restrict the report to one test path (e.g. `--test <path>`), as the skill's step for skipped tests starts from exactly this question.
- Got: the test was cut by `… 41 more (--limit 0 for all)` in the PERMA-FAILS section, so a grep for it on the default output found nothing, which reads like "it did not fail".
- Workaround: `--limit 0` into a file (1,500+ lines for this push), then grep.
