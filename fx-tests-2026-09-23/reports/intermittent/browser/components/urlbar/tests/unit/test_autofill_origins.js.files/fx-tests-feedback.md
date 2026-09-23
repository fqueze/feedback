## `fx-tests task`: how many of this job's failures share one message?

- Command: `fx-tests task eLE855-CRDuEzH08IzbeEQ.0 --profiles`
- Question: are the 307 failing tests one job-wide breakage (same message) or many separate failures?
- Expected: a short "by message" summary before the per-test list, e.g. `304x NS_ERROR_FILE_CORRUPTED ... [nsIPrefService.readUserPrefsFromFile]`, `1x [test_ODA : 40] 65 == 66`, ...
- Got: 307 per-test entries (1,248 lines with `--limit 0`), each repeating the message; the job-wide pattern only shows by scrolling.
- Workaround: `--limit 0 --full-messages > file`, then `rg -c` on the message.
