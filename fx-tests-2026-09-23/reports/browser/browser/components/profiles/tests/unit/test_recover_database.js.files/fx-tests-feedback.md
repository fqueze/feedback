## `fx-tests failures --message` does not search crash signatures, and says nothing about it

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown" --tests`
- Expected: the tests with that failure (it is the test's only failure mode in `fx-tests test`, listed
  under Issues as `CRASH child process hang at shutdown`).
- Got: "No failure matched ... Check --path, --message and --component for typos."
- Workaround: `fx-tests crashes --harness xpcshell` lists it as a signature (19,349 crashes, 503 tests).
  The no-match message could point at `fx-tests crashes --signature` when the text is a crash
  signature.

## `fx-tests test <path> --bugs` prints no bug section at all when there is none

- Command: `fx-tests test browser/components/profiles/tests/unit/test_recover_database.js --bugs`
- Expected: a "Bugs" section, saying "none" if none names the test.
- Got: the same output as without `--bugs`; nothing says whether the search ran and found nothing.

## Question: "which thread and state was the hung child in, across all of this test's dumps"

- Command: a loop over the 143 `fx-tests crash <task> <dump> --all-threads --frames 0` of
  `--task-ids`, grepping each for the process name and a few frames.
- `fx-tests test <path> --task-ids` gives one minidump per task, but there is no way to get the
  process type (here always "GPU Helper") and the top Gecko frame of each dump in one table; with
  143 dumps of the same signature, that table is the question.
