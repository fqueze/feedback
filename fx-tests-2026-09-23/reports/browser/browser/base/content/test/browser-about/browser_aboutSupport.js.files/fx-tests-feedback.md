## Question: why does this failing job have no per-test profile? (browser_aboutSupport.js)

- Command: `fx-tests task ExEifRfaSV-uUBbR0VY_rQ --profiles` (and `b5qm5HH2SruuBF3E8FtG6w`, other windows11-32 jobs)
- Expected: the reason there is no profile, next to the failure.
- Got: `No failing test named a per-test profile in this job.` and `(+1 more message for this test; --messages to see them)`. Only `--messages` shows the reason: `failed to upload profile: [Exception... "Out of Memory" nsresult: "0x8007000e (NS_ERROR_OUT_OF_MEMORY)" ...]` — which it was in all 7 windows11-32 rejection failures of this test.
- Workaround: re-run each task with `--messages`.
- What the output could have shown: under `--profiles`, a "profile upload failed: <message>" line for a test whose failure messages include `failed to upload profile`.
