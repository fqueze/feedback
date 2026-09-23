## Question: did function X run anywhere in this time range? (markers whose stack contains X)

- Context: to show `ToolboxController.js!setCanRender` never ran between a toolbox open and the test's click (and did run in an earlier open), in two CI profiles where samples are ~10 per 100 ms.
- Command I had to use: `thread markers --has-stack --list --limit 0 --json` in a `zoom push`, then `marker info <150 handles> --json` per chunk (1,447 and 1,423 markers), then a Python scan of `stack.frames[].nameWithLibrary`.
- Expected: `thread markers --stack-search setCanRender` (or `--search stack:setCanRender`), listing matching markers, or a count of zero.
- Got: `--search` matches only name/category/payload fields.
- Cost: about 2 minutes per window plus a script; the zero-hit answer depends on the script being right. Note that `frames[].name` holds bare function names (`open`), so matching `toolbox.js!open` needs `nameWithLibrary`, which is easy to get wrong.

## `--search` does not match the text `--list` prints for Runnable markers

- Command: `profiler-cli thread markers --search "priority: Idle" --list` (and `--search "Idle (0)"`), zoomed on a range holding `Runnable … WritableSharedMap::IdleFlush - priority: Idle (0)`.
- Expected: that marker, since the help says a bare term matches payload values and an unrecognized field is matched literally.
- Got: `No markers match the specified filters.` The printed label is built from the fields `name`, `priorityName`, `priority`; only `priorityName:Idle` matches. With a comma list (`TimeoutExecutor,IdleRequestExecutor,priority: Idle`), the dead term silently matched nothing while the others matched.
- Workaround: `--search "name:Runnable"` and grep the list output.
- Suggestion: match the formatted label that `--list` prints too, or warn when a `field:value` term names no field present on any marker in view.
