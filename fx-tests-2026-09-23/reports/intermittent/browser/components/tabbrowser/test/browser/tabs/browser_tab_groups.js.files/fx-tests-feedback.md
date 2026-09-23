## Which assertion actually failed in these runs (browser_tab_groups.js)

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_tab_groups.js` (and `--json --task-ids`)
- Expected: the Issues block to name the failing assertion, `arrowscrollbox is still scrolled to start - false == true` (TEST-UNEXPECTED-FAIL).
- Got: `104x FAIL handleEvent() was unable to perform a11y checks on hidden node ... tab-group-label` as "first failure per run", for every FAIL run, and for the TIMEOUT tasks too. That message is a `SimpleTest.todo(false, ...)` from AccessibilityUtils `a11yWarn`, i.e. TEST-KNOWN-FAIL, printed in every run including passing ones. The JSON `taskIds[].message` carries the same todo, so the real message is not in the published data at all.
- Workaround: loop `fx-tests task <id> --messages` over 27 of the 94 FAIL tasks and aggregate (all 27 had the arrowscrollbox assertion), then 21 more for the TIMEOUT ones. ~50 fx-tests calls.
- What would have answered it: skip TEST-KNOWN-FAIL / todo messages (and `changed preference:` warnings, which `task --messages` also lists as failures) when picking a run's first failure message.
