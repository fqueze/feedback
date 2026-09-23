## Question: which other tests fail in the same jobs as this one?

- Command: `fx-tests test dom/events/test/test_moz_mouse_pixel_scroll_event.html --task-ids --limit 0`, then the same for `test_wheel_zoom_on_form_controls.html` and `test_mouse_events_after_touchend.html`, then `comm` over the task IDs.
- Expected: `fx-tests test <path>` to say which tests co-fail in the same jobs (e.g. "test_wheel_zoom_on_form_controls.html also failed in 28 of these 28 jobs"). That is the first leaker/shared-cause signal, and `fx-tests task` shows it only one job at a time.
- Got: per-test task lists only; I had to script the intersection (28/28 co-fail with `test_wheel_zoom_on_form_controls.html`, 0/28 with `test_mouse_events_after_touchend.html`).
- Workaround: `rg -o` the task IDs out of three `--task-ids --limit 0` outputs and `comm -12`.
