## `profile markers --search` does not match the text it prints for a DOMEvent

- Command: `profiler-cli profile markers --search 'keyup - ' --session browser_alt_keyup_in_content.js-2`
- Expected: the `DOMEvent keyup - browser@14da701c0` and `keyup - body@...` markers, since `keyup - browser@...` is exactly the text the list prints for them.
- Got: `No markers match the specified filters (searched 19 threads).`
- Workaround: `--search keyup`, which also returns every other marker mentioning the test file name (`browser_alt_keyup_in_content.js`), so the DOMEvents had to be picked out by eye.
- The printed label is composed from eventType + target; searching the label as printed would make the obvious query work.

## `--session` is rejected before the subcommand (review-browser_alt_keyup_in_content.js)

- Command: `profiler-cli --session review-alt_keyup-1 thread markers --category Test --search browser_alt_keyup_in_content --list --limit 0`
- Expected: the option applies to the command, as a global option would in most CLIs.
- Got: `error: unknown option '--session' (Did you mean --version?)`
- Workaround: put `--session` after the subcommand. Accepting it in either position, or naming the right position in the error, would save the retry.
