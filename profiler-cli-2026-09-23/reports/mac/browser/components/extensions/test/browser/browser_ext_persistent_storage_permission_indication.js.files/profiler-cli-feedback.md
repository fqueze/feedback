# profiler-cli feedback

## `profile info --search` with a space matches nothing

- Command: `profiler-cli profile info --session <s> --search "Parent Process"`
- Expected: the parent process and its threads (the guide says it filters by process/thread name).
- Got: `Processes and threads matching 'Parent Process':` and an empty list; `--search Parent`
  works.
- Workaround: search one word.
