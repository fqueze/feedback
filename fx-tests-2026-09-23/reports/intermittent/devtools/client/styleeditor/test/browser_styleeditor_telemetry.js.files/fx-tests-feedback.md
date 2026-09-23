## Question: "was this a perma-fail that has since stopped?"

- Command: `fx-tests test devtools/client/styleeditor/test/browser_styleeditor_telemetry.js`
- Expected: a verdict that the test failed every run on the a11y-checks configs from 09-11 to 09-13, then stopped failing on 09-15. In other words a perma-fail, now fixed.
- Got: "Verdict: intermittent ... worst is ...a11y-checks at 39.0% (146/374)". That rate divides three days of 100% failure by three weeks of runs, most of them after the fix. The only hint is "recent 0.0%" in the table.
- Workaround: `--history --config <config>`, which shows 0 passes on 09-11..09-13 and 0 failures from 09-15. The verdict could compute the rate over the days since the last step change, or say "perma-failed between X and Y, no failures since Y".
