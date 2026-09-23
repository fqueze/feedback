## `fx-tests test <path>` verdict says "intermittent" for a permafail that stopped

- Command: `fx-tests test devtools/client/styleeditor/test/browser_styleeditor_telemetry.js`
- Expected: a verdict naming the step change, e.g. "failed every run on *-a11y-checks from 2026-09-11 to 2026-09-14, no failures since 2026-09-15".
- Got: "Verdict: intermittent ... worst is ...a11y-checks at 41.5% (146/352)", plus "Daily counts are not flat". Only `--history --config a11y-checks` showed 0 pass / all fail until the fix, then all pass.
- Workaround: ran `--history` with `--config` for the failing config.
