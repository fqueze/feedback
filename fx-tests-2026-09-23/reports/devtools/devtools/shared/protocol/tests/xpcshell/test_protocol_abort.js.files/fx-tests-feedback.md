## Question: "was this failure a job-wide infra collapse?"
- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg.0 --profiles`
- Expected: a hint that 1152 of 1552 tests timing out in a 2-minute run is a harness/infra abort (the job ended with `PermissionError: [WinError 5] Access is denied` from CreateProcess, and "Following exceptions were raised").
- Got: the list of 1154 failing tests, no mention of the harness exception that ended the run.
- Workaround: loaded the resource-usage profile and searched for "Traceback". The harness `Following exceptions were raised:` / traceback lines could be surfaced in the task summary.
