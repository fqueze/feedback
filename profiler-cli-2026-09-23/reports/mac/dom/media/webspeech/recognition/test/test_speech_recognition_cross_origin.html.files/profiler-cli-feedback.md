## Log markers from the test harness print "(empty)" instead of their message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` on a content GeckoMain (mochitest-plain per-test profile, task B6cfeE5CQVe45WdHhUiANQ).
- Expected: the `INFO` rows to show their message, e.g. `add_task | Entering cross_origin_track_is_blanked`.
- Got: `m-1740  INFO  t=2m33s  instant  ✗  [(empty)] INFO: (empty)` for every INFO row; the message is only in `--json` under `data.message` / `fields[].value` (its `formattedValue` is `(empty)`).
- Workaround: `--json` and read `data.message`.

## Times in `--list` are rounded to the second past 1 minute

- Command: same as above. Rows read `t=2m33s`, so the start of a task and the moment a 15 s wait began cannot be told apart from neighbouring markers without `--json` (`start` is in ms with full precision).
- Workaround: `--json`.

## A `Format::Seconds` field is printed divided by 1000

- Command: `profiler-cli profile markers --search "parakeet word" --limit 0 --session <s>` (task B6cfeE5CQVe45WdHhUiANQ, `-2` profile).
- Expected: the label `"mmhmm" @ 14.880s conf 16%` — the marker's `audioStartS` is 14.88, declared `MS::Format::Seconds` in `ParakeetWordMarker` (dom/media/webspeech/recognition/SpeechRecognitionParent.cpp).
- Got: `"mmhmm" @ 0.015s conf 16%`; the right value was only in `--json`. Every word of every session reads `@ 0.000s`/`@ 0.015s`, which hides where in the stream a word sits. Not checked whether profiler.firefox.com shows the same, i.e. whether this is profiler-cli or the profiler's own reading of the `seconds` format.
- Workaround: `--json`.

## The default session directory is not writable in this sandbox

- Command: `profiler-cli load <url> --session <s>`.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error message itself suggested `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`, which worked, and `profile-link.py` honours it. Cost one round trip; the brief could say it.
