## Question: "what happened in the 16 ms between these two ticks, 74 s into the profile?"

- Command: `profiler-cli thread markers --list --limit 60 --session <s>` after `zoom push 74.3,74.6`
- Expected: timestamps precise enough to order markers inside a 300 ms zoom.
- Got: every marker past one minute printed as `t=1m14s`, so the list could not say which markers fell between the tick at 74.498 s and the one at 74.5146 s.
- Workaround: `--json` and a Python one-liner printing `start/1000` to 4 decimals. The default list could print seconds with ms precision (`t=74.515s`) as it does below one minute.
