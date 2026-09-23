## `--issue` numbers change with `--since`

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_contentscript_context.js --task-ids --issue 2 --limit 0 --since 3`
- Expected: issue 2 = the TIMEOUT row, as in the unfiltered output I had just read (1 = SKIP tsan, 2 = TIMEOUT).
- Got: "--issue 2 is a SKIP row": with `--since 3` the rows reorder (1 = TIMEOUT, 2 = SKIP), so the number from the full-window output points at another row.
- Workaround: re-read the Issues block with the same `--since` first, then use `--issue 1`.
- Would help: accept a text filter (`--issue TIMEOUT`), or print the Issues block before the task list so the number can be checked in the same output.

## The failing runs of one mode, by where they stopped

- Question: of this test's 849 timeouts, how many are the content-process crash on the `Overwriting an existing document channel` assertion, and on which configs?
- Command: `fx-tests errors --harness xpcshell --test <path> --group-by message --task-ids` for 2026-09-21 showed the assertion in only 1 job, yet 2 of the 5 logs from that day that I downloaded myself had it (fQvGq3jrRWiUl44KaMMvFQ, AR9_lwuqQy6yF86T9hpJlA). I had to download 25 live_backing.log files and grep them with a script.
- Could have shown: per failing run, the last TEST-PASS / `Starting <subtest>` line before the timeout and any `Hit MOZ_CRASH` in the replayed log; that splits one TIMEOUT row into its mechanisms.
