## Question: which tests are behind this crash signature?

- Command: `fx-tests crashes --harness xpcshell --signature "storage::Service::Observe" --limit 0`
- Expected: the tests hitting the signature (the text says "327 crashes, 28 tests").
- Got: only the counts. `fx-tests failures --message "Service::Observe"` / `--message "Storage connection"` find nothing, because crashes are not failure messages there.
- Workaround: `--json`, where `rows[].tests[]` has the list. The text output could print the tests the way `failures --tests` does.

## Question: what did the crash say (its MOZ_CRASH message)?

- Command: `fx-tests test <path>`, `fx-tests task <taskId> --profiles`, `fx-tests crash <taskId> <dump>`
- Expected: somewhere, the `Hit MOZ_CRASH(...)` line / the preceding stderr line (`Storage connection not closed: places.sqlite`). It names the leaked resource and is the key to the diagnosis.
- Got: only the signature `@ mozilla::storage::Service::Observe`. For one run the dump did not symbolicate and it counts as a separate issue `CRASH [Unknown]`, though its log has the same `Storage connection not closed: places.sqlite ... mozStorageService.cpp:701` line.
- Workaround: load the job's resource-usage profile and search the `output` markers.

## `fx-tests crash` transient failure

- Command: `fx-tests crash aUCB22nLRPyagzqigfJong.0 444d199d-8687-4435-ac40-5969c0dc5655`
- Got: `response is not valid JSON: Unexpected end of JSON input — This looks transient`. This is the `[Unknown]` crash, so the dump JSON is probably just not there. Maybe it should exit 4 (gone) rather than suggest a retry.
