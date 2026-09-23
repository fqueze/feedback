## Question: how common is "remote browser crashed while on" in CI?

- Command: `fx-tests errors --day 2026-09-01 --message "remote browser crashed"` (also `"crashed while on"`, and the default day)
- Expected: the jobs and tests that logged this console.error. Job NZLFMO7qS1Gu9zcEJqKeiA logged it (`console.error:` / `remote browser crashed while on` / `about:blank` on three lines), and its resource-usage profile has it as an `output` marker.
- Got: "No markers matched", with nothing saying whether multi-line console.error messages or `output` lines are indexed at all.
- Could have shown: the matching count, or a note that this kind of line is not in the errors file.
