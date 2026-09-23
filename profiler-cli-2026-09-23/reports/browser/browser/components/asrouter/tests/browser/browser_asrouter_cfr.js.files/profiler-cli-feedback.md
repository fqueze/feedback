
## Question: when was this runnable dispatched? (review, 2026-09-22)

- Command: `profiler-cli thread markers --search "<task id from the Runnable marker>" --list --limit 0`
  (e.g. `047f822c3609c9e10` from `Runnable nsXULPopupShownEvent ... task: 047f822c3609c9e10`).
- Expected: `marker info` on a `Runnable` marker to name its dispatching
  `TaskController::AddTask` (same flow, the last one before it), with time and index.
- Got: nothing links them. The task id is reused all over the profile, since it comes from an
  address, so the search returns dozens of AddTask/Runnable markers. I had to zoom in and pick
  the last AddTask before the run by hand.
- Workaround: zoom to the range, search the flow id, take the last `TaskController::AddTask`
  before the `Runnable`. The original report missed this and wrote that "the profile does not
  record when a runnable is dispatched".
