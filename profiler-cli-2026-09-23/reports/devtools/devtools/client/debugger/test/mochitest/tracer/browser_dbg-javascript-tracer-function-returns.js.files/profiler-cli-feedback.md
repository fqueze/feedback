## Question: "which markers in this range have a stack through function X?" (here: did any DOM change come from `ToolboxController.setCanRender`?)

- Command: `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0 --json` for the handles, then `profiler-cli marker info <254 handles>` and a grep of the text for `setCanRender`.
- Expected: a way to filter markers by a frame of their captured stack, e.g. `thread markers --search SetNeedStyleFlush --stack-search setCanRender --list`, or `--has-stack <func>`.
- Got: `--search` matches the name, category and payload only; stacks are shown one marker at a time.
- Workaround: dumped every marker's info (6,000 lines) and grepped it, plus a python summary of the top JS frames per marker.
- What the output could have shown: the list rows of the matching markers, or a count of them, like `thread samples --search` does for samples.

## Question: "did any idle-priority task run in this range?" (review-browser_dbg-javascript-tracer-function-returns.js)

- Command: `profiler-cli thread markers --session review-fr-1 --search "priority: Idle" --list --limit 0` (zoomed to 1.60,2.00)
- Expected: the `Runnable` rows whose displayed text reads `priority: Idle (0)`.
- Got: `No markers match the specified filters.`, while 3 such rows exist in that range (`WritableSharedMap::IdleFlush - priority: Idle (0)` at 1.814 s and 1.837 s). The row text is formatted from `priorityName`, and `--search` does not match the formatted text. A false "none" here nearly went into the review as a fact.
- Workaround: `--search name:Runnable --list --limit 0` to a file, then `rg "\(0\)"`.
- What the output could have shown: match the text the row displays, or say in `--help` that `priorityName:Idle` is the field to search.

## `--session` placement

- Command: `profiler-cli --session review-fr-1 thread markers ...`
- Expected: the global option accepted before the subcommand.
- Got: `error: unknown option '--session' (Did you mean --version?)`
- Workaround: put `--session` after the subcommand.
