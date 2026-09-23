## `test --bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_tabGroups_move.js --bugs`
- Question: "which bugs name this test?"
- Expected: a line such as `Annotated bugs: none` in the text output.
- Got: the same output as without `--bugs`, with no bugs section at all. I could not tell whether the lookup had run and found nothing, or had not run.
- Workaround: `--json`, where `annotatedBugs` is `[]`, then a Bugzilla quicksearch (which found two closed tracking bugs, bug 1966823 and bug 1968897, and nothing open).

## `test --task-ids` and `task` disagree on the job's chunk number

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_tabGroups_move.js --task-ids --limit 0`
- Expected: the chunk shown by `fx-tests task`, i.e. the same job name.
- Got: `Je25kLerSRKXX6BD9QIojQ.0  test-macosx1500-aarch64/opt-mochitest-browser-chrome-1`, while `fx-tests task Je25kLerSRKXX6BD9QIojQ --profiles` says `test-macosx1500-aarch64/opt-mochitest-browser-chrome-9`.
- Workaround: used the name from `fx-tests task`.

## `task --messages` lists a TEST-KNOWN-FAIL next to the failures, unlabelled (review-browser_ext_tabGroups_move.js)

- Command: `fx-tests task Ki44sN4XQku0yx0ucySiwA --messages` (same for Je25kLerSRKXX6BD9QIojQ and e_MQRlzZQDWWQeyMkp4zeg)
- Question: "which of this test's messages are failures?"
- Expected: `changed preference: sidebar.verticalTabs.dragToPinPromo.dismissed` either left out or marked as known-fail/todo, as `messages` in `--json` already does (it is only in `allMessages`).
- Got: `1x changed preference: ...` listed under `FAIL — 1 failing execution of 2` in the same format as the real `TEST-UNEXPECTED-FAIL`, with no status.
- Workaround: read `checkPreferencesAfterTest` in `browser-test.js` (`todo: !gConfig.comparePrefs`) and found the marker in the profile, where it is `TEST-KNOWN-FAIL`. Three calls.
