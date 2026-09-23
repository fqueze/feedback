## A filtered `--list --limit 0` has no output-size guard

- Command: `profiler-cli thread markers --session <s> --search "setTimeout() for,...,name:task,..." --list --limit 0`
- Expected: the dozen markers I meant, or a warning that the filter matched far more than a list can show.
- Got: `name:task` is a substring match, so it also matched every `TaskController::AddTask` marker: 82,196 rows, 9.9 MB of output, dumped into my context.
- Workaround: dropped that term and used `--limit 60`.
- Suggestion: when a `--list --limit 0` result exceeds a few thousand rows, print the count and the top matching marker names instead of the rows, and require an explicit flag to print them all.
