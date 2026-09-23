## Marionette tests are invisible to `fx-tests test`

- Command: `fx-tests test toolkit/xre/test/marionette/xdg_config/test_xdg_config_new_existing.py`
- Expected: per-config rates, history, task IDs for a Marionette (Mn) test.
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...` (exit 2).
- Workaround: `curl https://treeherder.mozilla.org/api/failuresbybug/?startday=...&endday=...&tree=all&bug=2001846`
  for the annotated occurrences (177 since 2026-05-26); no pass counts available at all, so no rate.

## `intermittent --bug` names no test for Marionette failures

- Command: `fx-tests intermittent --bug 2001846`
- Got: `Tests named, per annotated job (none: no occurrence carried a TEST-UNEXPECTED-FAIL line ...)`.
- Marionette reports `TEST-UNEXPECTED-ERROR`, not `-FAIL`, so every Marionette bug shows no test.

## Question: "what failure message did each annotated job of this bug have"

- Needed to split the bug's occurrences into failure modes (FileNotFoundError on which file vs
  AssertionError). No command answers it for Marionette; I downloaded 57 `live_backing.log`s and
  grepped the first `TEST-UNEXPECTED` line naming the test.
- What could have shown it: `fx-tests intermittent --bug <N> --messages` listing the first
  unexpected line per occurrence (Treeherder's `failuresbybug` `lines` field was empty here).
