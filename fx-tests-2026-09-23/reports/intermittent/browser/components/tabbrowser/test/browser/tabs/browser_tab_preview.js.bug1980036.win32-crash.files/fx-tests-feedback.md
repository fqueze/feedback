## Question: the tests behind this crash signature

- Command: `fx-tests crashes --harness mochitest --signature NS_ABORT_OOM`
- Expected: the tests that crashed with that signature (the text says "55 crashes, 11 tests").
- Got: only the count row. The `tests` array with the per-test counts is only in `--json`.
- Workaround: `--json` and read `.rows[].tests`. With a single-row result (a `--signature` filter), the text output could list the tests under the row.

## Question: are these unsymbolicated crashes the same stack as the symbolicated one?

- Command: `fx-tests crash fujONjImTxWcBoeLq-nz9Q.0 9d107df2-f9df-42ad-9fe5-d350f7554bd5`
- Expected: a symbolicated stack, or a hint why not.
- Got: `xul.dll + 0x...` frames only. `--raw` shows the xul.dll module has `debug_id` all zeros and `debug_file` empty, so it can never be symbolicated; the default output does not say so. 94 of this test's 115 crashes are like that, each with its own `xul.dll + 0x…` signature, so `fx-tests test` splits one crash into ~90 Issues rows.
- Workaround: compared frame count and offsets frame by frame with the symbolicated `NS_ABORT_OOM` dump by hand. Saying "module has no debug id: unsymbolicatable" in the header, and grouping such signatures in `fx-tests test`'s Issues (e.g. as "xul.dll + <offset> (unsymbolicated, N distinct)"), would have saved that.

## Question: how many of this config's failures got a per-test profile?

- Command: `fx-tests test <path> --config windows11-32 --profiles --limit 0`
- Expected: per failing run, its per-test profile URL, or that none was uploaded.
- Got: 431 `resource-usage:` URLs; the note that per-test profiles are under `fx-tests intermittent --test <path> --profiles` is the last line of ~970. I first read the absence of per-test URLs as "none uploaded", which was wrong. `fx-tests intermittent --test <path> --profiles` then lists annotated jobs (task ids), not the per-test profile URLs either.
- Workaround: `fx-tests task <id> --messages` on a sample of 20 tasks, grepping for `failed to upload profile` vs `profile uploaded in`. A per-config count of "profile uploaded / upload failed / none" in `fx-tests test`, or the hint at the top of the `--profiles` block, would have answered it.
