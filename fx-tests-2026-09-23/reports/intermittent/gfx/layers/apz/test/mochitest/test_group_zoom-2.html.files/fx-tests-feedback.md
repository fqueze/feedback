## `fx-tests task --profiles` does not list the hang profile of an "application timed out" failure

- Command: `fx-tests task cXC_WwPnQY6-TTry6JPt8w.0 --profiles` (also aQBmyUHvQPauieZHxGtmDg.0, VEBOuNvPT0ax4DjfaNI1UA.0, Eymp4YyFSReusQemMkkSOg.0, FA5F5jjVTRiT2eixymbpaQ.0, RaTzd8_QT7yB8H_Io_dOlg.0, JnF0MnLXTJGgB6-CG9mPYg.0, FpyEFt6NTaeR0OKJDTpbbg.1)
- Expected: for the TIMEOUT "application timed out after 370.0 seconds with no output", the profile the harness captured during the hang (it sends SIGUSR1/SIGUSR2 and uploads `public/test_info/profile_0_<pid>.json`), listed under the failing test like per-test profiles are.
- Got: only the resource-usage profile, and "No failing test named a per-test profile in this job" on the Linux ones. The hang profile exists in every one of the 8 timeout jobs of this test.
- Workaround: found `Sending SIGUSR1 to pid ... start the profiler` and an `artifact profile_0_4821.json — 648MB` marker in the resource-usage profile, then listed the task's artifacts with curl. That hang profile was the only one holding the answer.

## Question: "which landing were these failing jobs running with?" needs push times

- Command: `fx-tests test gfx/layers/apz/test/mochitest/test_group_zoom-2.html --task-ids --issue 3` then `fx-tests task <id>` for each.
- Needed: the push time of each failing job's revision, to line the 8 timeouts up against landings and backouts (6 of them turned out to be exactly inside the two short windows when bug 2059195 was on autoland).
- Got: the day of the job and the revision, but not the push time, and hg.mozilla.org answered 406 to json-rev/raw-file requests at that moment.
- Workaround: `https://treeherder.mozilla.org/api/project/<repo>/push/?revision=<rev>` for each revision, then `git log --grep`. Printing the push time (and the push's first commit subject) next to each task id would have made the correlation visible directly.

## `fx-tests test` Issues line says "Test exceeded time limit" for "application timed out after 370.0 seconds with no output"

- Command: `fx-tests test gfx/layers/apz/test/mochitest/test_group_zoom-2.html`
- Expected: the verbatim message, which says the harness killed the whole browser (no per-test timeout fired), a different situation from a JS-harness test timeout.
- Got: `TIMEOUT Test exceeded time limit`; the real message only shows in `fx-tests task`.
- Workaround: `fx-tests task <id>`.

