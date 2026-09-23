## Question: "on Android, which slot did the failing execution use?"

- Command: `fx-tests task <taskId> --profiles`
- Expected: for a remote xpcshell failure, the slot (`$iN`) of each failing execution, since `Could not kill left-over process` failures cluster in one slot per job.
- Got: outcomes only; the slot has to be derived from the resource-usage profile's `launch_application` lines and the scheduling order.
- Workaround: profiler-cli `--json` plus a script.
