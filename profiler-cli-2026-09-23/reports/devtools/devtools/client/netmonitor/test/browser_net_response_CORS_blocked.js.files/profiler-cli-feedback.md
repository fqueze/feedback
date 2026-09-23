## Question: the exact time of a marker

- Command: `profiler-cli marker info m-48 m-72 m-13 --session <s>`
- Expected: the start/end in ms (for example 234980.34), so the order of markers in the same second can be read.
- Got: `Time: 3m55s (instant)`, rounded to the second. Every event of interest fell in one second, so I could not order them.
- Workaround: `marker info ... --json` and a Python one-liner printing `start`/`end`.
