## `task --profiles` does not say which run a lone profile belongs to

Command: `fx-tests task e9lg-CFQRPuu-qQgIuUEgw --profiles`
Expected: with "2 failing executions of 2" and one profile, say whether that profile is the first run or the retry, and that the other one failed to upload.
Got: `profile_browser_tab_splitview_contextmenu.js.json` listed, with no mention of which run it is. Only `--messages` showed `failed to upload profile: … NS_ERROR_OUT_OF_MEMORY`, and only loading the profile (the test started 2 s into the session) showed it was the retry.
Workaround: `--messages`, then load the profile and check where the test starts.

## Question: "a profile that contains a passing run of this test"

The brief says to compare with a passing run, but none has its own profile. A later test in the same manifest and job that failed has a per-test profile that covers this test's passing run.
What I did: listed the tests after this one from browser.toml, ran `fx-tests test` on 14 of them to find Windows opt failures, then ran `fx-tests task` on each candidate to check that this test passed there. That took about 20 commands.
What would answer it: `fx-tests test <path> --passing-profiles [--config X]`, listing jobs where the test passed and a later test in the same browser session uploaded a profile.
