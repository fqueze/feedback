## Which manifest variant of a dupe-manifest test failed

- Question: this test is in both `browser.toml` and `browser-srd.toml` (`dupe-manifest=true`, differing only by the `browser.settings-redesign.enabled` pref). Do both variants fail, or only the SRD one?
- Command: `fx-tests test browser/components/preferences/tests/telemetry/browser_usage_telemetry_support_link.js` (and `--task-ids`)
- Expected: runs/failures split per manifest, or the manifest next to each task ID.
- Got: one merged row per config; no manifest anywhere.
- Workaround: loaded a job's resource-usage profile and searched for `Running manifest:` markers. Four failures read were all `browser-srd.toml`; whether `browser.toml` ever fails is left unanswered.
