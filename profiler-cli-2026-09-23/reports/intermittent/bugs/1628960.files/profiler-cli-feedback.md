
## review-1628960: `marker stack` on a C++ warning marked as having a stack

- Command: `profiler-cli marker stack m-254 --session review-1628960-1` (C++ warning `IPC Connection Error: [Child][PCompositorManagerChild] ...`, shown with ✓ in `thread markers --list`).
- Expected: the JS/label stack where the warning was raised (which actor's ActorDestroy triggered the teardown), or no ✓ if there is nothing useful.
- Got: `[1] unknown!null`.
- Workaround: none; fell back to log ordering. A ✓ that leads to a single unknown frame could be shown as "no usable stack" in the list.
