# fx-tests feedback (browser-test_rust_ingest.js)

## `--bugs` prints nothing when no bug is found
- Command: `fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_rust_ingest.js --bugs`
- Expected: a "Bugs" section, or an explicit "no sheriff-annotated bug names this test".
- Got: output identical to the run without `--bugs`; I had to rerun and search Bugzilla myself to be sure it had not been truncated.
- Workaround: Bugzilla REST `summary=test_rust_ingest`.

## Question: "which process is this minidump from, and what was it spinning on?"
- Command: `fx-tests crash OF4Vs6-lQBi8SkH3UTQ4JA.0 E5B2839D-AC91-415F-B312-831DCE0A8987` (and `--json`)
- The .extra annotations are not shown: `CrashSignatureOverrideForTesting`, `XPCOMSpinEventLoopStack = gpu: nsThread::Shutdown: Renderer`, `GPUProcessStatus`, `StartupTime`. Here they said at once that it was the GPU process, killed by ProcessWatcher, spinning on the Renderer thread's shutdown.
- Workaround: `curl .../public/test_info/<dumpId>.extra`.

## Question: "which message is behind many failures" for a crash signature
- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown"`
- Expected: the 503 tests that `fx-tests crashes` lists under that signature.
- Got: "No failure matched". The signature is only reachable through `fx-tests crashes`, which does not list the tests behind it.

## Question: "on which workers did the failing jobs run?"
- Command: `fx-tests test <path> --task-ids --limit 0`
- Needed: the worker ID next to each task ID. The 26 macOS failures are on 9 of 77 workers, the same machines failing again over weeks, which is the main clue here.
- Workaround: one `curl .../task/<id>/status` per task.
