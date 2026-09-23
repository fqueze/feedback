## `thread markers --search name:test --list --limit 0` never returned

- Command: `profiler-cli thread markers --session test_transitions.html-1 --category Test --search "name:test" --list --limit 0 | grep -v TEST-PASS | tail -15` on a per-test mochitest-plain profile whose content main thread has 1,041,578 markers (232,344 in category Test).
- Expected: the `test` markers (one per test in the manifest), or at worst the 232k Test markers after a minute or two.
- Got: no output after more than 20 minutes; the daemon still answered `status`, so the client or the output stage was stuck. Killed it.
- Workaround: `--search "test_transitions.html"` (7 s, 248 markers), since the TEST-* markers of mochitest-plain carry the test path in a field.
- Question it should answer: "the `test` markers of this profile" — `name:` matching the marker name exactly, or a warning before streaming 200k+ rows, would have saved the wait.

## Sub-microsecond timestamps need `--json`

- Question: "how many ns after start + 2 s was the vsync the check read?" Needed the `VsyncTimestamp` marker times to 1e-6 ms, and `thread markers --list` prints them rounded to the ms (`t=54.691s`).
- Command: `profiler-cli thread markers --search name:VsyncTimestamp --list --limit 0 --json | python3 ...` to print `start` with 9 decimals and differences.
- Could have shown: an option for full-precision times in `--list`, or `marker info` printing the raw start/end in ms.
