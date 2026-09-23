## Question: "does this error message appear anywhere else in the window?"

- Command: `fx-tests errors --day <d> --message "too much recursion"`, repeated for 2026-09-01, 09-02, 09-10, 09-15 (and 09-20 by default); 2026-08-31, the failure's own day, has no errors file.
- Expected: one call answering whether a message specific to one failing job (`InternalError: too much recursion` from `codemirror6.bundle.mjs`) occurs in other tests or days.
- Got: one slow download per day; had to loop and eyeball each. The guide says there is no multi-day aggregate, so this is by design, but a `--since`/`--days` loop inside the command (even if slow) would save the scripting and the repeated boilerplate output.
- Workaround: shell loop over days.
