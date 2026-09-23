## Question: which assertion or panic is behind this crash signature?

- Command: `COLUMNS=300 fx-tests test browser/base/content/test/webrtc/browser_devices_get_user_media_camera_preview.js --task-ids --limit 0 --full-messages`
- Expected: a signature that tells this crash apart from other Rust panics. Either the MOZ_CRASH reason (`assertion failed: info.ignore_missing_pipeline`) or the first frame past the panic machinery (`webrender::scene_building::SceneBuilder::push_iframe`).
- Got: `CRASH @ <mozglue_static::panic_hook as core::ops::function::Fn<`. That is cut at `Fn<` even with `--full-messages`, and it is the same for every Rust panic in the tree. `fx-tests crash <task> <dump>` shows the stack, but not the MozCrashReason annotation either.
- Workaround: ran `fx-tests crash` on each task and read frame 9 by hand, then got the assertion text from the log in the resource-usage profile.
