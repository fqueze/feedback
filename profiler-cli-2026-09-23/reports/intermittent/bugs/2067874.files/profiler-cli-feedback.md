## Question: which code point is in this marker message? (review-2067874)

- Command: `profiler-cli thread markers --session review-2067874-1 --search surrogate --list --limit 20`
- Expected: the message shown so that a lone surrogate can be seen, e.g. as `\ud842`.
- Got: lone surrogates print as U+FFFD. In DSCq, `--search test_css_supports_variables --list --limit 0` also wrote a raw NUL byte from a test message, and `rg` then treated the output as binary ("binary file matches").
- Workaround: `marker info <m-…> --json`, and `rg -a`. The text output could escape lone surrogates and control characters.
