## Question: which tests are behind a crash signature?

Command: `fx-tests crashes --harness mochitest --signature mozalloc_handle_oom`
Expected: the tests that crashed with this signature (the `tests` column says 25), at least the top few with counts.
Got: one row, `225 crashes / 25 tests / @ mozalloc_handle_oom`, no test names.
Workaround: `--json`, where `rows[].tests[]` lists every test with its count. The default output could list the top tests under each signature (the answer here was that nearly all 25 are in one directory, `browser/components/aiwindow/ui/test/browser/`, which is the whole diagnosis).

(Same question again, with `--since 8 --signature oom`: needed `--json` and a script to see which tests are still crashing with OOM signatures.)

