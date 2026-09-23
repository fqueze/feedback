## `function annotate` contradicts itself on line info

- Command: `profiler-cli zoom push 7.575,7.583 --session bcs-finalized-1` then
  `profiler-cli function annotate f-2980 --session bcs-finalized-1` (profile
  `KRhYU0waTpKjxgGTOhCBrw/.../profile_browser_conversation_starters.js.json`, parent main thread).
- Expected: either the sample is attributed to a line, or it is not.
- Got: the header says `0 of 1 samples have line number information`, while the table puts
  that one sample on line 411 (`--json`: `"samplesWithLineInfo": 0` and line 411
  `"totalSamples": 1`). I could not tell whether line 411 is real or a placeholder.
- Workaround: cited line 411 as "attributed by profiler-cli", and relied on other evidence.
