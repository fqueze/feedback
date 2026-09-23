## Question: which tests are behind this crash signature?

- Command: `fx-tests crashes --harness xpcshell --signature "storage::Service::Observe" --limit 0`
- Expected: the 28 tests behind the signature, with their counts (the row says `tests 28`).
- Got: one row, `327  28  0  @ mozilla::storage::Service::Observe`, and no test names.
- Workaround: `--json` has `rows[0].tests[]` with every test path and count; I scripted over it.
  The default output could print the top tests under each signature (or with `--tests`).

## Question: what did the crash print just before dying? (the unclosed DB name)

- Command: `fx-tests task Zpd4gnquTymlQZYn-wH47g.0 --messages --full-messages`
- Expected: for a CRASH row, the last stderr line before the crash
  (`Storage connection not closed: places.sqlite ... Hit MOZ_CRASH()`), which is what names the culprit for
  this signature. Also the `StorageConnectionNotClosed` crash annotation in `fx-tests crash`.
- Got: only `@ mozilla::storage::Service::Observe`; `fx-tests crash` shows no annotations.
- Workaround: found it as an `output` marker in the resource-usage profile with profiler-cli.
