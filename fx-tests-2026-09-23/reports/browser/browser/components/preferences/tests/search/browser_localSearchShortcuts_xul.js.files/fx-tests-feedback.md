## Which tree a config's failures and passes come from

- Question: three shippable/opt configs (`linux2404-64-shippable/opt-...-no-nv`, `...-swr-a11y-checks`, `windows11-64-25h2-shippable/opt-...-no-nv`) fail ~42% while the other shippable configs fail 100%. Are the passes all on autoland and the failures all on mozilla-central (the test only fails on the `nightly` update channel)?
- Command: `fx-tests test browser/components/preferences/tests/search/browser_localSearchShortcuts_xul.js --since 7 --coverage`
- Expected: a per-tree (repo) split of runs/fails per config, or a `--tree`/`--repo` filter.
- Got: per-config totals only; `fx-tests test --help` has no repo option. `fx-tests intermittent --bug` shows trees, but only for annotated (failing) jobs, so it cannot say where the passes ran.
- Workaround: none; left as inferred in the report.
