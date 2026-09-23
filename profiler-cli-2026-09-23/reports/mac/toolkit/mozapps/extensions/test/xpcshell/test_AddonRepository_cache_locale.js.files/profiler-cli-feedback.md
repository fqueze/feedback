# profiler-cli feedback

## `--search` does not match the text the list prints for Runnable markers

- Command: `profiler-cli thread markers --session <s> --search 'Idle (0)' --list` and
  `--search 'priority: Idle'`
- Expected: the Runnable markers the list prints as `... - priority: Idle (0) task: ...`.
- Got: 0 markers. The printed text is a label built from fields; the searchable value is
  `priorityName:Idle`, which I found only through `marker info --json`.
- Workaround: `--search 'priorityName:Idle'`.
- Suggestion: match bare terms against the printed label too, or say in the "no markers" message
  which fields were searched.
