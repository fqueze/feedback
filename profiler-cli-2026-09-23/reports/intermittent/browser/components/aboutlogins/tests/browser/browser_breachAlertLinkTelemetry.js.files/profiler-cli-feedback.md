## Question: a link for a new observation, in a session opened from a report's link (review)

- Command: `profile-link.py --session review-...-1 --marker m-1518`, after `profiler-cli load '<profiler.firefox.com/from-url/... link>'` as review-brief.md instructs ("`profiler-cli load <link>` opens it at its thread").
- Expected: a link to the marker, since the session knows the underlying artifact URL.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link."
- Workaround: stop the session and reload the raw Taskcluster URL. The review brief and profile-link.py disagree; profile-link.py could unwrap `from-url/` itself.
