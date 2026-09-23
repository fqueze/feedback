## Question: which bug covers this failure?
- Command: `fx-tests test browser/components/mozcachedohttp/test/unit/test_url_parsing.js --bugs`
- Expected: a Bugs section, even if it says "no bug names this test", and ideally bugs whose summary matches the failure message (here bug 1994039, "Network cache reported memory consumption is not at 0").
- Got: the ordinary report, with no Bugs section at all, so it is unclear whether the search ran.
- Workaround: took the bug number from a sibling report on the same assertion.

## Transient artifact fetch
- Command: `fx-tests crash XfxIFkIkQqehR9CXEO9Q8A.0 f6e852d9-4d38-4f69-b814-7b8e78e8476b`
- Got: `response is not valid JSON: Unexpected end of JSON input ... This looks transient`. Not retried; minor.
