## The task IDs of the passing jobs of one test on a try push

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --test dom/canvas/test/webgl-conf/generated/test_2_conformance2__textures__misc__tex-input-validation.html --all-jobs --task-ids` (also with `--json`)
- Expected: the task IDs behind each row of the per-configuration table, in particular the opt jobs that passed, so their profiles can be compared with the failing debug ones.
- Got: the table only (jobs / passed / failed per config); `--task-ids` is silently ignored with `--test`, and the JSON has no task IDs either.
- Workaround: query the Treeherder REST API for the push's jobs.

## Which crash dump belongs to which test

- Command: `fx-tests task GQYf3gDHRlep__mE302Jmg`
- Expected: each CRASH row showing the signatures of the crashes that test caused.
- Got: `tex-input-validation.html  CRASH — 4 failing executions of 4` with signatures `ErrorImplementationBug<>` and `gfx::Log<1, CriticalLogger>::WriteLog`, and `texsubimage2d-4gb-wasm-memory.html  CRASH` with the same two. The harness assigns every GPU-process dump found at the end of a manifest run to the last test that ran: `texsubimage2d-4gb-wasm-memory.html` PASSed (its `test` marker) and got the first run's two dumps; `tex-input-validation.html`, last in the retry, got both retry dumps, one of which (`WriteLog`, "Unexpected error from driver: DoCopyTexSubImage(...) -> 0x0506") is `copy-texture-image-same-texture.html`'s. Only the per-test stdout (`[pid] Assertion failure ...` lines under each test) says whose crash is whose.
- Workaround: read the `output` markers per test in the resource-usage profile and match the GPU pids.
- What could show it: when a CRASH marker's test also has a `test` marker with status PASS, or when a CRASH row's signature does not appear in that test's own output, flag the attribution as end-of-manifest dump collection.

