## Question: "the minidump of this crash"
- Command: `fx-tests task WDC6mbZUTXGoxz1P-riDNA --profiles` (and `fx-tests test <path> --task-ids`)
- Expected: the minidump ID for the `CRASH @ mozilla::(anonymous namespace)::RunWatchdog` it reports, ready for `fx-tests crash`, and the `profile_shutdown_hang_<pid>.json` artifact.
- Got: the crash signature only; no minidump ID, no mention of the shutdown-hang profile. `--json` has no minidump field either.
- Workaround: `curl .../task/<id>/runs/0/artifacts` to find `public/test_info/697f8bb6-....dmp`, then `fx-tests crash <task> <dump> --harness mochitest`.
- Could show: the dump ID next to the CRASH line, and any `profile_shutdown_hang_*.json` in the profile list.
