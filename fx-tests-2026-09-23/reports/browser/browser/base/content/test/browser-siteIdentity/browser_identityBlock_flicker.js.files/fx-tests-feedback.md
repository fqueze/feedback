## `--bugs` prints nothing when there is no annotated bug

- Command: `fx-tests test browser/base/content/test/siteIdentity/browser_identityBlock_flicker.js --bugs`
- Expected: a line such as "No sheriff-annotated bugs name this test".
- Got: exactly the same output as without `--bugs`, so it is impossible to tell "no bug" from "the flag was ignored or the lookup failed". `--json` shows `"annotatedBugs": []`.
- Workaround: `--json` and read `annotatedBugs`.

## `--config` does not filter the Issues block

- Command: `fx-tests test browser/base/content/test/siteIdentity/browser_geolocation_indicator.js --config test-windows11-32-25h2/opt-mochitest-browser-chrome --exclude-config shippable,msix,standalone,asan,ccov`
- Expected: Issues counts restricted to that config.
- Got: the header says `117 fail`, but Issues row 1 says `156x`. The Issues block seems to count every config, so the failure modes of one config cannot be read.
- Workaround: none from the text output. I used the per-config `failCount` from `--json`, which does not split by message.

## Question: how often does one failure message hit a manifest, per config, across all its tests?

- Question: the failure rotates between five tests of one manifest (whichever runs 30 s after startup). I needed, per config, the number of failures with this message summed over the manifest's tests, next to the number of runs of the manifest.
- Commands: `fx-tests test <each test, old and new path> --json`, summing `configs[].failCount` with a script. `fx-tests failures --message ... --tests` gave the per-test totals but "records no job names".
- What would have answered it: `fx-tests failures --message <m> --path <dir>` with a per-config breakdown, or `fx-tests test <manifest dir>` aggregating over its tests.
