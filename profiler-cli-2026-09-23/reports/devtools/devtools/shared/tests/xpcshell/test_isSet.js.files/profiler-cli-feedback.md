## Question: "the exact time of a marker, to zoom tightly around it" (test_isSet.js)

- Command: `profiler-cli marker info m-4 --session test_isSet.js-2` (resource-usage profile of task ZtblArCLSx6O6KzfEkL1YQ)
- Expected: the marker's start time with ms precision, so I can `zoom push` onto the 3 ms full-log replay that sits between `Begin of full log` and `End of full log`.
- Got: `Time: 1m51s (instant)`, rounded to the second. Hundreds of other replayed log lines share that second.
- Workaround: `marker info ... --json` piped through a python script to read `start` (110886.915). The default text could print `1m50.887s`, or the start in ms.

## Exclusion-only `--search` returned 0 markers in a zoom where a positive search found 18

- Command: `profiler-cli thread markers --session test_isSet.js-2 --search "-name:CPU Use,-name:Memory,-name:IO,-name:NetIO,-name:Sampling Interval,-name:test,-Begin of full log,-End of full log,-Timed out and was force-killed,-xpcshell return code" --list --limit 60`, run after `zoom push 111,143`
- Expected: the remaining INFO/ERROR markers in the range. `--search "Retrying,..."` in the same zoom found m-2731 `INFO Retrying tests that failed when run in parallel.`, which matches none of those exclusions.
- Got: `0 markers (filtered from 5394)`. The same kind of exclusion-only search, with only the `-name:` terms, worked in another zoom (1006 markers). I suspect the bare `-<text>` terms, or `-name:test` matching the payload `name` of Text markers, but the output does not say which term removed everything.
- Workaround: searched positively for the terms I guessed.
