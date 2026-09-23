## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test devtools/client/debugger/test/mochitest/sourcemaps/browser_dbg-sourcemapped-toggle.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names this test (Bugzilla has four resolved 2019-2020 bugs naming it).
- Got: the same output as without `--bugs`, with no bug section and no "none" line, so "no bug" looks the same as "flag ignored".
- Workaround: Bugzilla REST `bug?summary=browser_dbg-sourcemapped-toggle`.

## `task --profiles` does not list the shutdown-hang profile or the minidump

- Command: `fx-tests task TvIPAAEpR92HGf5U1ZXQfw.0 --profiles`
- Expected: the job's other diagnostic artifacts for the failing test: `profile_shutdown_hang_1615.json` (318 MB) and the minidump `7f0449d5-1ca4-85b4-2859-5c313787763a`, since the test row says `CRASH, TIMEOUT` and `@ mozilla::(anonymous namespace)::RunWatchdog`.
- Got: only the per-test profile and the resource-usage profile. I found the shutdown-hang profile and the minidump ID in the resource-usage profile's `artifact`/`output` markers.
- What could have shown it: in the CRASH row, the minidump ID ready for `fx-tests crash`, and the shutdown-hang profile URL.
