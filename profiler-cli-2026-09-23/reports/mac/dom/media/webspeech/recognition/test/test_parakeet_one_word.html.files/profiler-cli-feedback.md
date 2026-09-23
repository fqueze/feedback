## Log markers (mochitest `info()`) show `(empty)` for their Level and Message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` on the content main thread of a mochitest-plain profile, then `profiler-cli marker info m-175 --session <s>`.
- Expected: the `info()` text, e.g. `INFO add_setup | Entering setup`, `[diag +3456ms] final result #2: " yeah"`.
- Got: every INFO row reads `[(empty)] INFO: (empty)`; `marker info` shows `Level: (empty)`, `Message: (empty)`. In `--json`, `fields[].value` holds the text and `formattedValue` is `"(empty)"`.
- Also: `thread markers --category Test --search diag` matches nothing, although the message values contain `[diag ...]`: search apparently looks at the formatted value too.
- Workaround: `--list --json` and printing `fields[].value` with a script. The question was "what did the test log with info(), in order, next to its assertions": the test's own log is half missing without it.

## (review) One payload field across every marker of a name, in order

- Question: "the `totalFedMs` (and `wordsCommitted`, `endOfUtterance`) of every `parakeet_capi_stream_feed` marker on this thread, in order", to count the feeds after `<EOU>` and read where the stream ended.
- Command: `profiler-cli thread markers --search parakeet --list --limit 0 --session <s>` on a `Parakeet` thread.
- Got: each row shows the marker's label (`fed 80ms, queued 80ms, 0 word(s)`), which omits `totalFedMs`; the field is only in `marker info <handles> --json`.
- Workaround: collected the handles from the list, passed them all to `marker info --json`, and printed the fields with a script. A `--fields totalFedMs,wordsCommitted` option on `--list`, or a column per payload field, would answer it directly.
