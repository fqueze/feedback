## `--session` is rejected before the subcommand

Command: `profiler-cli --session review-browser_toolbox_content_modal_overlap.js-1 thread markers --category Test --search browser_toolbox_content_modal_overlap --list --limit 0`

Expected: the session option accepted in front of the subcommand, as a global option, like `git -C`.

Got: `error: unknown option '--session' (Did you mean --version?)`.

Workaround: put `--session` after the subcommand (`profiler-cli thread markers --session <id> ...`). Costs one call per agent that guesses the other order.
