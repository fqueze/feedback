## Question: find a DOMEvent by the text the list prints for it

- Command: `profiler-cli thread markers --search 'fullscreen-nav-toolbox,browser.fullscreen.autohide,Browser:OpenLocation,beforeselect,fullscreen - window' --list --limit 0 --session <id>`
- Expected: the `DOMEvent ... fullscreen - window` row the list itself prints (it exists: `m-1145 DOMEvent t=3.051s fullscreen - window`).
- Got: every other term matched; `fullscreen - window` matched nothing, because the printed label is composed from `eventType` and the target, and search matches neither the composed label nor across fields.
- Workaround: `--search name:DOMEvent --list --limit 0` piped through `rg 'fullscreen'`.
- Could have shown: search matching the row's printed description, or a hint that `eventType:fullscreen` is the field to use.
