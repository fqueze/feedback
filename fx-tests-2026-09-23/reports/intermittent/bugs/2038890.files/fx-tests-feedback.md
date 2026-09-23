## 2038890: manifest-level LSan shutdown leak is invisible outside `intermittent --bug`

- Commands: `fx-tests test browser/components/aiwindow/ui/test/browser/browser.toml` → "No test path … contains"; `fx-tests failures --harness mochitest --message FileSystemBackgroundRequestHandler --tests` → "No failure matched"; `fx-tests errors --message FileSystemBackgroundRequestHandler` → no markers.
- Expected: rates per config for the `LeakSanitizer | leak at …` failure attributed to the manifest (it makes 21 jobs orange in 7 days).
- Got: only `fx-tests intermittent --bug 2038890` sees it, as sheriff annotations; no denominator.
- Workaround: runs of a test in the manifest on that config (`fx-tests test <a test in it> --config linux2404-64-asan --history`) as the denominator.
- Question: "how often does this shutdown leak happen, on which configs, out of how many jobs".

## 2038890: `task --profiles` does not show the job's non-test failure nor which browser session it hit

- Command: `fx-tests task DE4kDmklSbqD6bksBdvBDw --profiles`.
- Got: the three failing tests and their profiles; the LeakSanitizer failure (the one that made the job orange) is not listed, nor whether it came from the first browser session or the harness retry session.
- Workaround: downloaded all 21 logs and scripted over `TEST-START | Shutdown` / `==pid==ERROR: LeakSanitizer` lines; found 5 of 21 leaks happened in the retry session (a fresh browser running 1–3 tests), which localizes the trigger a lot.
- Question: "the job's failures not attributed to a test (leaks, shutdown crashes), and which browser session (first run or retry, and the tests it ran) each came from".
