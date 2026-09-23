## `task --profiles` does not say which manifest each execution and profile belongs to

- Command: `fx-tests task HVKybJaiTiKLYd0tcoLbBw --profiles`
- Got: `FAIL — 2 failing executions of 4`, `Passed when the harness reran it.`, and two profiles `profile_test_contextmenu_nested.xhtml.json` / `profile_test_contextmenu_nested-2.xhtml.json`.
- The natural reading (and the brief's: "two profiles for your test mean the retry failed too") is that the retry failed. It did not: the test is listed in two manifests (`chrome.toml` and `chrome-nova.toml`, `dupe-manifest = true`), it failed once in each in manifest order, and each retry passed. Only the resource-usage profile's `test` markers showed that (manifest marker, FAIL, `retry`, PASS, next manifest, FAIL, `retry`, PASS).
- What would have answered it: per execution, the manifest it ran from and whether it was the initial run or the retry, e.g. `chrome-nova.toml: FAIL (profile …) → retry PASS; chrome.toml: FAIL (profile …-2) → retry PASS`. Same for `fx-tests test`: the "×2" on unchunked mac jobs is two manifests, not a failed retry.
