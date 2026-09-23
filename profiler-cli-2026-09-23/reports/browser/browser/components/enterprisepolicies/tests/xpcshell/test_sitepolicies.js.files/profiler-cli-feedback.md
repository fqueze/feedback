## JS frame suffix `[N]` reads like a line number but is not one

- Command: `profiler-cli marker stack m-24 --session <id>` (T3JNy0u1SpGTXiMZKzGI4Q, profile_test_sitepolicies.js.json)
- Expected: a module top-level frame giving its source line, e.g. `ContextualIdentityService.sys.mjs:1126` (the `Services.dirsvc.get("ProfD")` call).
- Got: `moz-src:///toolkit/components/contextualidentity/ContextualIdentityService.sys.mjs[3]` on Windows and `...[41]` on macOS for the same code; `head.js[21]` for a call at head.js line 174. Nothing says what N is. The report under review quoted `[3]` as if it meant something.
- Workaround: the line came from the task's live_backing.log exception text, and from reading the source.
