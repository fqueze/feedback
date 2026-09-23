## `task --profiles`: which execution a profile is from (browser_ext_port_disconnect_on_window_close.js)

- Command: `fx-tests task b_7uPWjIQE-MqD-D_HpNRg --profiles --limit 0`
- Expected: for a test with `FAIL — 2 failing executions of 2`, a profile per execution, or a note that one is missing and why.
- Got: one profile URL, unsuffixed (`profile_browser_ext_port_disconnect_on_window_close.js.json`), with nothing saying which execution it is. It is the retry's: the first run's upload failed (`failed to upload profile: [Exception... "Out of Memory" ... NS_ERROR_OUT_OF_MEMORY]`, win32), so the retry took the unsuffixed name, breaking the "-2 is the retry" convention.
- Workaround: loaded the resource-usage profile and read the `failed to upload profile` and `profile uploaded in ...` markers per execution.

## `test --bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_port_disconnect_on_window_close.js --bugs`
- Expected: a `Bugs` section, saying "none" when there is none.
- Got: the same output as without `--bugs`; no line says whether the lookup ran and found nothing.
- Workaround: took it as "no bug names this test".

## Question: do all of this test's failing jobs also have test X failing?

- Command: `fx-tests test <mine> --task-ids --limit 0` and `fx-tests test <leaker> --task-ids --limit 0`, then grep/sort/comm over the two outputs.
- What could answer it: a `--with <other test path>` (or `--co-failures`) on `fx-tests test` listing, per failing job, whether the other test failed in it too, or the other tests failing most often in the same jobs.
