## `task --messages` counts distinct messages, not occurrences

- Command: `fx-tests task ZnpEvNYJR265Mcda_YWPIw.1 --profiles --messages`
- Expected: the count in front of each message to be how many times it was logged in the job, so
  "did only the first task of browser_878452 fail, or all three?" can be answered from it.
- Got: `1x Uncaught exception in test - TypeError: can't access property "dataTransfer", sess is null`,
  while the test's own profile shows that message three times (once per `add_task`, at
  t=18.181, 18.187 and 18.195). The `1x` read as "the later tasks passed", which was wrong.
- Workaround: loaded the per-test profile and listed the `TEST-UNEXPECTED-FAIL` markers.

## Question the default output did not answer: "does this job have a per-test profile for my test?"

- Command: `fx-tests task <taskId> --profiles` on four TSan tasks, one at a time.
- The TSan jobs never upload per-test profiles ("No failing test named a per-test profile in this
  job."). `fx-tests test <path> --task-ids` could mark which listed tasks have one, so picking a
  profile does not take one `task` call per candidate.
