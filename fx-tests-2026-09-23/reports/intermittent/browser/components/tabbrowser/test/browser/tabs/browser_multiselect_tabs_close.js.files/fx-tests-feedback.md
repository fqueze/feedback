## `test --task-ids` lists some jobs twice

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_multiselect_tabs_close.js --task-ids --limit 0`
- Expected: one row per job, with `×2` when the job saw the failure twice (as it does for HyNjbtg6SGS8V_gRX-hXsg and others).
- Got: `Qm8SEyiIRYCaZ6OFRxRytw.0`, `FB5aCKeRQQCEu_YkzIjHzg.0`, `dGACFHtiRf-vsblzRTRx0g.0` and `QmBFi5MiSsyBr2Ar6RQ7Tw.0` each appear on two separate rows without `×2`, and the header says "28 jobs" for 32 rows.
- Workaround: none needed; cost a double take when picking a job whose retry also failed.
