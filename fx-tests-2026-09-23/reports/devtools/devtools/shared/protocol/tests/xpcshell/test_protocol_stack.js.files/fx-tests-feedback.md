## Question: which bug were this test's failing jobs starred on?

- Command: `fx-tests test devtools/shared/protocol/tests/xpcshell/test_protocol_stack.js --bugs`, and `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles`
- Expected: the one failing job (PyUxuOpdQj2b3T3XyQOKpg.0) was starred by a sheriff on bug 1991833 ("High frequency win-msix PermissionError: [WinError 5] Access is denied"); one of these should say so.
- Got: `--bugs` printed no bug (the bug does not name the test, only the job-wide traceback), and `task` does not show the job's classification.
- Workaround: Bugzilla quicksearch for "msix Access is denied", then Treeherder `api/jobs/?task_id=...` and `api/project/autoland/bug-job-map/?job_id=591877755` to confirm the star.
- What the output could show: in `task`, the job's Treeherder classification and bug; in `test --bugs`, the bugs its failing jobs were starred on, not only bugs whose summary names the test.
