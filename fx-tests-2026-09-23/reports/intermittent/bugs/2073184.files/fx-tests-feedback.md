## `fx-tests task` refuses the partial resource-usage profile of a max-run-time kill

- Command: `fx-tests task Muk0F7ZlTYGMre7jnZK0vQ --profiles`
- Expected: per-test outcomes and durations of the job, even if incomplete: for a `[taskcluster:error] task aborted - max run time exceeded` bug that is the whole question (which tests got slower, what the machine was doing).
- Got: "its profile is a partial stream rather than a finished document and this tool does not read that format." The stream is JSONL with the same marker payloads (test, CPU Use, IO), so it is readable.
- Workaround: parsed live_backing.log and the JSONL stream with scripts.

## Question with no command: "how long did each job of this config take, and on which manifests?"

- For a job-level timeout, I needed per-job wall time, the manifest subset each job ran (MOZHARNESS_TEST_PATHS), and the setup vs test split, over days. Nothing in fx-tests gives job durations; `manifests` has one day only and no --day. Workaround: Treeherder jobs API + Taskcluster task definitions + perfherder-data-mozharness-actions.json, scripted.
- Also useful: machine-wide IO write rate and iowait per job from the resource-usage profile (it was the discriminating signal here: 1-4 MB/s normally, 22-28 MB/s in the regression window, capped at ~12 MB/s with 9-43% iowait on the jobs that timed out).
