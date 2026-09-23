## Question: which failure messages came from the first run, which from the retry, and which run the per-test profile belongs to

- Command: `fx-tests task BxelJHxIQDKpJFUkmgtM7g --profiles --messages` (same for the other 4 jobs of browser_suppressTips.js)
- Expected: the messages grouped per execution (first run / harness retry), and the profile URL labelled with the execution it came from.
- Got: `FAIL — 2 failing executions of 2`, one flat list of 5 messages, and a single profile URL. The brief's rule "two profiles mean the retry failed too" does not hold here: the retry failed, but the first run failed only through the post-shutdown leak check (`leaked 1 window(s) until shutdown`), which runs after the browser exited, so no per-test profile exists for it. Nothing in the output says the one profile is the retry's.
- Likewise `fx-tests test <path>` lists "5x leaked 1 window(s) until shutdown" and "5x updateAppliedTime ... 0 != 0" as two independent Issues, when every job had exactly one of each: the leak in the first run, the assertion in the retry.
- Workaround: loaded the job's resource-usage profile and listed its `test`/`FAIL` markers to see the two executions and which messages each one produced.
