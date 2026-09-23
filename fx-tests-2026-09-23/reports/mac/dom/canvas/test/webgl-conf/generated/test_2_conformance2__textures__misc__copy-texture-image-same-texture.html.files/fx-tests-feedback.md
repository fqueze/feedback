## `fx-tests task` says no per-test profile, but the job uploaded two for a CRASH

- Command: `fx-tests task GQYf3gDHRlep__mE302Jmg --profiles` (same for `NoU1wXy5TWqKi2oCHpo-kw`), and `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids`.
- Expected: the per-test profile URLs of `test_2_conformance2__textures__misc__copy-texture-image-same-texture.html`, whose status is CRASH.
- Got: "No failing test named a per-test profile in this job.", and only the resource-usage profile in the `try` output. The resource-usage profile has `FAIL` markers "profile uploaded in profile_test_2_conformance2__textures__misc__copy-texture-image-same-texture.html.json" (t=5m50s) and "...-same-texture-2.html.json" (t=6m31s), and both artifacts exist under `public/test_info/`.
- Likely cause: the "profile uploaded in" message is attached to a FAIL marker after the test's CRASH marker; for crashed tests the tool may not associate it (or it expects a `.js.json` suffix; here it is `.html.json`).
- Workaround: `thread markers --search "profile uploaded" --list` in the resource-usage profile, then build the artifact URL from the named file.
- Also: the CRASH rows carry no message at all for this test, while the crash signature (`[GFX1]: Unexpected error from driver: DoCopyTexSubImage(...) -> 0x0506`, `MOZ_CRASH(GFX: An assert from the graphics logger)`) is in the resource-usage profile's `output` markers.

## Crash signatures are shown under the wrong test when the harness processes minidumps at the end of a manifest

- Command: `fx-tests task GQYf3gDHRlep__mE302Jmg --messages --full-messages` (same for `NoU1wXy5TWqKi2oCHpo-kw`).
- Expected: the `@ mozilla::gfx::Log<1, mozilla::gfx::CriticalLogger>::WriteLog` signature under `test_2_conformance2__textures__misc__copy-texture-image-same-texture.html`, whose run printed the `MOZ_CRASH(GFX: An assert from the graphics logger)` and "Found unexpected crash dump file .../0A6D9B7C-7257-4DD1-A33C-4FF45BC0ABF9.dmp".
- Got: that test's row has no signature; the WriteLog signature is listed under `test_2_conformance2__wasm__texsubimage2d-4gb-wasm-memory.html` (the last test of the manifest, which itself logged PASS) and, for the retry, under `test_2_conformance2__textures__misc__tex-input-validation.html`. The resource-usage profile's `Crash` markers (t=6m27s and t=6m38s, "processing 2 crashes") carry minidump IDs 0A6D9B7C-... and 52CA119C-..., which match the "Found unexpected crash dump file" INFO lines logged under the copy-texture-image-same-texture runs.
- What would have answered it: joining each `Crash` marker's Minidump ID to the test whose "Found unexpected crash dump file <id>.dmp" line names it, rather than to the Crash marker's own Test Name field. Without that, a test that passed (texsubimage2d-4gb-wasm-memory) is reported as CRASH with another test's signature.
- Workaround: `thread markers --search "type:Crash" --list`, then `marker info` for the Minidump ID, then `--search "<minidump id>"` to find the test that left it.
