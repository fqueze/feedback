## Question: average machine CPU over a time window of the resource-usage profile

- Question: was the machine saturated during the 5 s vsync wait (job time 113.9-119.0 s)?
- Command: `profiler-cli thread markers --session <resource-usage session> --search 'CPU Use' --list --limit 0 --json` and a script averaging `cpuPercent` over the window.
- What the output could have shown: `fx-tests task <id> --profiles` could print, per failing test, the machine CPU (avg/max) during that test's execution, which is the saturation question every timeout triage asks.
