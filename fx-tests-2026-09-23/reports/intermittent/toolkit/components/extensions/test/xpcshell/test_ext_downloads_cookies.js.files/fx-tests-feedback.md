## `task --profiles` omits the profile line for a failure without saying why

- Command: `fx-tests task TokNmASPT_CgCwRiq1EiwA --profiles` (also IlUOlTggSvyhTAj1qUS8tw, IkGUt1GwS6WjlTizGSvB9Q, FYSUkiPmRguHNXkdXdvdJg, LImIHXTbQpujvWLoncYG0w, aK-FHxZxT8ytYa0NjL-Eug)
- Question: "is there a per-test profile for this failure mode (NS_ERROR_FILE_DIR_NOT_EMPTY in a cleanup function), and if not, why not?"
- Expected: either a `profile <url>` line, or a line saying no profile was uploaded for this failure (e.g. "no profile: the failure message names none").
- Got: the test's block has the message and no profile line, while TIMEOUT blocks in the same job do. Nothing says whether none exists or the tool did not find it.
- Workaround: downloaded the job's live_backing.log and read the replayed test log to confirm it names no profile. Checked six tasks of that failure mode before concluding none of them has one.
