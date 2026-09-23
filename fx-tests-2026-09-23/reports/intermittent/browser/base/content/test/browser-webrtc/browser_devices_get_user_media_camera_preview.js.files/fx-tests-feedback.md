## `fx-tests test <path> --bugs` printed no bugs

- Command: `COLUMNS=220 fx-tests test browser/base/content/test/browser-webrtc/browser_devices_get_user_media_camera_preview.js --bugs`
- Expected: a bugs section naming bug 2060263. Its summary names the test under its old path, `browser/base/content/test/webrtc/`, which was renamed on 2026-09-16. Ideally also bug 1979669, which carries 10 of the test's 13 crashes in the window.
- Got: the same output as without `--bugs`, with no bugs section and no "none found" line.
- Workaround: `fx-tests intermittent --bug 1979669 --limit 0`, intersected by hand with `--task-ids`. The question was "on which bugs were this test's failures starred".

## Which test step each crash happened at

- Question: at which step of the test did each of the 13 crashes happen (last TEST-PASS/INFO before the crash)?
- Command: `fx-tests test <path> --task-ids --limit 0` gives task + minidump IDs only.
- Workaround: load all 13 resource-usage profiles in profiler-cli and script over `thread markers --json`. The last log line before a CRASH, per task, in `--task-ids` output would have answered it without loading any profile.
