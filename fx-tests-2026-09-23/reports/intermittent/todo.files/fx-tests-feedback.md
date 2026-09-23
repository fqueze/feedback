# fx-tests feedback (list agent, 2026-09-22)

## "The test file an `unknown` bug names"

- Command: `fx-tests intermittent --harness unknown --since 21 --limit 0 --json`
- Expected: a path for bugs whose summary names a test of a suite fx-tests does not read.
- Got: `test: null` on all 699 rows. 402 of them name a test file in the tree: WPT URLs as served
  (`/fetch/api/crashtests/huge-fetch.any.worker.html` has source `.../huge-fetch.any.js`, `/_mozilla/` and
  `mozilla/tests/` meaning `testing/web-platform/mozilla/tests`), reftests relative to `layout/reftests`
  (`svg/text-layout-10.svg == svg/pass.svg`), GeckoView/Fenix junit classes
  (`org.mozilla.geckoview.test.AutocompleteTest#x[#y]` -> `.../AutocompleteTest.kt`), crashtests,
  marionette/pytest files (`a.py::TestX::test_y`), jit-tests (`tests\jit-test\jit-test\tests\...`), bare
  mochitest names (`test_mediarecorder_avoid_recursion.html`).
- Workaround: `todo.files/build_todo.py` resolves summaries against `git ls-tree origin/main`.
- Could show: a test column resolved against the tree for any suite, with the suite name.

## "Where the test a bug names lives now"

- Command: `fx-tests intermittent --harness mochitest --since 21 --limit 0 --json`
- Got: 32 of 444 `test` paths are not on main: 30 moved (the bug 2069131 renames of
  `browser/base/content/test/<dir>` to `browser-<dir>`, urlbar/preferences test reorganisations), 2 removed.
  Nothing in the output says so.
- Workaround: unique-basename lookup in the tree.
- Could show: a marker for a path gone from the published test lists, with the renamed path when known.

## "Which of these bugs are closed"

- Command: `fx-tests intermittent --harness mochitest --since 21 --limit 0` (text and `--markdown`)
- Got: no status column; `status`/`resolution` only in `--json`. Two restricted bugs (2074018, 2071379) come
  with `bugSummary: null` and nothing saying why.
- Could show: `(FIXED)` next to closed bugs, and "restricted" for bugs whose summary Bugzilla withholds.
