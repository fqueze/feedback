## Question: when was a bug last annotated (did it stop after a fix landed)?
- Command: `fx-tests intermittent --bug 2049056 --since 30 --limit 0`
- Expected: a "last seen" push time and tree in the header, next to "716 sheriff annotations on trunk, 2026-08-24 to 2026-09-22", or occurrences listed newest first.
- Got: the header gives the window's range, not the date of the last occurrence, so I needed all 716 occurrence rows to find it.
- Workaround: `... | sed -n '/Occurrences/,$p' | sort -k1,2 | tail -30`.
