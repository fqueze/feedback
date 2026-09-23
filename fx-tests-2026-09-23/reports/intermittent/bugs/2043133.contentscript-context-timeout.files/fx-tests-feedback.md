## Question: "which of this test's TIMEOUTs stop in which subtest?"

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_contentscript_context.js --task-ids --issue 2`
- Expected: some way to split the 839 `TIMEOUT Test exceeded time limit` runs, for example by the last `TEST-PASS` before the timeout, or by an assertion logged in the full-log block.
- Got: one Issues row for every timeout. Two different mechanisms fall under it: a fatal assertion in subtest 4, and a hang in subtest 1.
- Workaround: downloaded 28 more job logs (about 400 MB) and ran awk over each `Begin of full log` block. Of 48 logs, 27 were the assertion and 15 were the subtest 1 hang.
