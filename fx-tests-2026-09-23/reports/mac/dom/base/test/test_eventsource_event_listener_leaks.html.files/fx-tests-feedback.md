## `--bugs` prints nothing when it finds nothing

- Command: `fx-tests test dom/base/test/test_eventsource_event_listener_leaks.html --bugs`
- Expected: a "Bugs" section, even if it only says no bug in the window names the test; ideally the closed tracking bug (bug 1776977, "single tracking bug", RESOLVED INCOMPLETE 2026-05-25) flagged as closed.
- Got: output byte-for-byte identical to the run without `--bugs` (checked with `diff`), so "no bug" and "the flag was ignored" look the same.
- Workaround: `curl -sL "https://bugzilla.mozilla.org/rest/bug?summary=<test file name>"`.
