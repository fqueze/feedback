## Question: which failure messages came from the first run and which from the harness rerun?

- Command: `fx-tests task AKySeucETC2yR59OIaLDNw.0 --messages`
- Expected: messages split per execution (initial run vs rerun), since for a shutdown-leak test the two runs fail differently.
- Got: one merged list per test ("1x leaked window until shutdown [url = about:newtab]", "1x ... [url = chrome://browser/content/browser.xhtml]", ...). Nothing says the browser.xhtml leak is the first run's TEST-FAIL and the about:newtab leak is the rerun's TEST-UNEXPECTED-FAIL, which was the whole diagnosis here.
- Workaround: downloaded `live_backing.log` for 13 jobs and grepped TEST-START / TEST-FAIL / TEST-UNEXPECTED-FAIL lines by line number.
- What would have answered it: group `--messages` by execution, labelled "initial" / "rerun", with the status (TEST-FAIL vs TEST-UNEXPECTED-FAIL).

## Question: the leak's retention path (the "stack" of a ShutdownLeakPathFinder failure)

- Command: `fx-tests task <id> --messages`, `fx-tests test <path> --task-ids`
- Got: only the message text. The retention path, which names the leaker, is only in the log / errorsummary / the resource-usage profile's FAIL marker.
- Would help: print the `stack` field for `leaked window until shutdown` failures (it is short).
