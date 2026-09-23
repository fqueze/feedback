## `fx-tests test <path> --bugs` prints nothing when no bug is found

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_takeCensus_01.js --bugs`
- Expected: a line saying no bug names this test, or a list of bugs.
- Got: output identical to running without `--bugs`, so I could not tell "no bug" from "the flag did nothing".
- Workaround: Bugzilla quicksearch via curl.

## A job where the harness could not launch xpcshell reads as 1,152 TIMEOUTs

- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles`, then `fx-tests test <path>` (Issues: `TIMEOUT Test exceeded time limit`).
- Question: did this test time out, or did it never run?
- What the output could have shown: the job's harness traceback (`PermissionError: [WinError 5] Access is denied` from `launchProcess`, in the resource-usage profile), or a flag on jobs where most tests TIMEOUT at the same moment. Without that, each of these tests reads as a rare intermittent TIMEOUT of its own. The same pattern hit 4 other msix jobs in the window (ZtblArCLSx6O6KzfEkL1YQ, dnwE_TAATwiUK5fbCsoauw, Q6pDc4X0SYOXqgi4KIpuzA, LKys8j8iTQKTAN9hKv040g).
