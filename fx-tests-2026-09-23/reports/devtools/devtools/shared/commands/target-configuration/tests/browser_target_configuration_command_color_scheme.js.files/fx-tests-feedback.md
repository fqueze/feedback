## Question: did the try push exercise the `skip-if` it removed?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids`, then `--all-jobs --test <path>`
- Expected: for a test whose `skip-if` the push removed, a line saying whether any job on the push matched the removed condition (here `linux 24.04 x86_64 x11 debug http3`).
- Got: the default output never mentions the test, since it did not fail. `--all-jobs --test` lists 7 configs that all passed, and none of them is http3. To see that the push never ran the skipped config, I had to diff the manifest and compare job names by hand.
- Workaround: `git show <commit> -- browser.toml` plus reading the `--test` table.
