## `marker info --json` on a CompositorScreenshot prints the whole image, twice

- Command: `profiler-cli marker info --session S m-11503 --json` (to read `markerIndex` and `threadHandle`, as the review brief asks)
- Expected: the marker's index, thread, times and fields, with the image data truncated or left to `screenshots`.
- Got: the full base64 JPEG in `screenshot.url` and again in `screenshot.base64` (plus a truncated copy in `rawFields`), about 15 KB of output for one marker.
- Workaround: pipe through a script that drops `screenshot` and `rawFields`.
- What would have answered it: `screenshots` listing each image's `markerIndex` and thread, or `marker info` truncating `screenshot` like it does `rawFields`.
