## Question: "which configurations does each failure mode come from?"
- Command: `fx-tests test browser/components/profiles/tests/unit/test_selectable_profiles_lifecycle.js`
- The Issues list gives counts per message, and the config table gives counts per config, but not the cross product. Here the 5 Windows msix failures are all the TIMEOUT mode and the 656 macOS ones are the two other modes; learning that took `--task-ids --limit 0 --issue 3` and `--issue 4`.
- What would have answered it: a configs column (or top config) per row under Issues.
