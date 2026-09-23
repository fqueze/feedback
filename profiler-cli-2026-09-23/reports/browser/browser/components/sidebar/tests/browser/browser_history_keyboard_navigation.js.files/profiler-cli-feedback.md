## Question: "exactly when did these markers happen, relative to each other?" (profile longer than a minute)

- Commands: `profiler-cli thread markers --session browser-hkn-3 --category Test --search browser_history_keyboard_navigation --list --limit 0` and `profiler-cli marker info m-18 m-55 m-56 m-16 --session browser-hkn-3`
- Profile: 3m36s long (H9e0CaPXRUujWYR3fMbylw, Windows asan).
- Expected: millisecond timestamps, as in profiles under a minute (`t=38.588s`).
- Got: every marker in the list, and `Time:` in `marker info`, reads `t=3m24s` / `Time: 3m24s - 3m34s`, so the order of a keydown, a focus and a toggle within the same second cannot be read, nor can a `zoom push` range be chosen from them.
- Workaround: `marker info ... --json` and read `start`/`end` (ms), then `zoom push 203.60,203.95`.

## Question: "the DOMEvent markers of one event type" (review)

- Commands: `profiler-cli thread markers --session browser-review-hkn-1 --search mousedown --list --limit 0`, then `--search "name:DOMEvent,mousedown"`.
- Profile: KbxDPuhZRzuJL9GgZj2FXw, 861k markers on the parent main thread.
- Wanted: only the `mousedown - <target>` DOMEvent markers.
- Got: the bare term also matches `Preference Read` markers whose value mentions it (`dom.popup_allowed_events: change click dblclick auxclick mousedown ...`). The comma form ORs the terms, so it lists every DOMEvent of the session. Combined with a `Preference Read` + pref-name search, a loop of three such full-profile searches ran past 120 s and was killed.
- Workaround: the OR search piped through `rg "DOMEvent .*mousedown"`. An AND between terms, or matching the DOMEvent's event type (`type:mousedown`), would answer it directly.
