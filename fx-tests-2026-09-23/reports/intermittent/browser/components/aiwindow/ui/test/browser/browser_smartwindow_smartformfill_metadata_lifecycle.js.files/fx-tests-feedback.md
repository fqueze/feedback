## The tests behind one crash signature, in a directory

- Question: which aiwindow tests got the `mozalloc_handle_oom` crash after 2026-09-07 (it moved off my test)?
- Command: `fx-tests crashes --harness mochitest --path browser/components/aiwindow --signature mozalloc_handle_oom --since 15`
- Expected: the test names, since the table already prints a `tests` count (11).
- Got: only the count; the names are in `--json` (`rows[].tests[]`). `fx-tests failures --message oom|mozalloc --tests` found nothing either, as crash signatures are not failure messages there.
- Workaround: `--json`. A `--tests` flag on `crashes` (like `failures --tests`) would answer it.

