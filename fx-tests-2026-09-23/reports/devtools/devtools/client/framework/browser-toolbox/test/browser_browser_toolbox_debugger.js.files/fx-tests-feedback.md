## Question: did this test turn the job red even though it passed?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/framework/browser-toolbox/test/browser_browser_toolbox_debugger.js`
- Expected: some sign that each ASAN run of the test emitted a job-failing error line (`ERROR: AddressSanitizer: SEGV ...` from the test's intentional `BrowserTestUtils.crashFrame`), which mozharness counts as an error and turns into TBPL_FAILURE.
- Got: 21/21 "passed", 0 failed, on every config including both ASAN ones. That reads as "the skip-if can go", which is wrong: all 6 ASAN jobs have the sanitizer line in their Treeherder error lines.
- Workaround: a Python script over the Treeherder `jobs/?push_id=` and `jobs/<id>/text_log_errors/` APIs, then grepping the job's live_backing.log. The resource-usage profile does hold the line as an `output` marker inside the test's span.
- What could have shown it: a per-test "error lines emitted during this test's span" column/flag (the harness error-list matches: sanitizer reports, `PROCESS-CRASH`, panics) in `try --test` and `task`, marked separately from TEST-UNEXPECTED outcomes.
