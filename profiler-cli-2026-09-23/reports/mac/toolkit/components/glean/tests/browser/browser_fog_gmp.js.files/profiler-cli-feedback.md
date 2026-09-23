## `--search name:test` has no exact-match form

- Command: `profiler-cli thread markers --search "name:startupTelemetryIdleTask,name:fog.testResetFOG,...,name:test,..." --list --limit 0 --session browser_fog_gmp.js-2`
- Expected: the per-test `test` status markers (name exactly `test`).
- Got: 290 markers, mostly `Runnable` and `ExtensionSchemas` ones whose payload contains "test", which buried the markers I asked for.
- Workaround: `--category Test`. An exact-match operator (e.g. `name:=test`) would have saved a call.
