## `test --bugs` prints nothing when no bug is found
- Command: `fx-tests test browser/components/places/tests/browser/browser_bookmarksProperties.js --bugs`
- Expected: a "Bugs" section, saying "none" when empty.
- Got: the same output as without `--bugs`; no section at all. Only `--json` (`annotatedBugs: []`) told "no bug" from "query silently did not run".
- Workaround: `--json | jq .annotatedBugs`.

## `task --messages` mixes TEST-KNOWN-FAIL results into the failure list
- Command: `fx-tests task O5oFDzaSTJSeINejQth4yA --messages --full-messages`
- Expected: only the unexpected failures under "FAIL", or the todo ones marked as such.
- Got: `changed preference: sidebar.history.sortOption` and `Assertion count 2 is greater than expected range 0-0 assertions.` listed next to the leak, although browser-test.js logs both as `todo` (TEST-KNOWN-FAIL). Had to read the harness to find they are not failures.
- Workaround: read browser-test.js / the profile's TEST-KNOWN-FAIL markers.

## Question: "does this test only fail in jobs where test B fails too?"
- Commands: `fx-tests test <A> --task-ids --limit 0 --json` and the same for `<B>`, then a script intersecting task IDs and comparing `occurrences`.
- What would have answered it: a "fails together with" block in `fx-tests test <path>` listing the other tests failing in the same jobs, with the share of this test's failing jobs where they also failed. Here it was 224/224 mac debug jobs, same occurrence count every time: the whole diagnosis hinged on it.

## `test --task-ids --json`: `message` is null for some tasks
- Command: `fx-tests test browser/components/places/tests/browser/browser_bookmarks_change_title.js --task-ids --limit 0 --json`
- Got: `"message": null` for the 14 entries dated 2026-09-21, while older entries carry the message.
