# Optional natural-language query interpretation

Separate extension from embeddings. Use when the user wants phrases such as “notes tagged travel from January 2026.” Start with a narrow deterministic parser; do not imply general language understanding.

## Prompt

Read CLAUDE.md, docs/decisions.md, and the current search API. Add a small, documented natural-language filter feature only for agreed forms. Preserve the original query and show the proposed filters before applying them. The user can edit or remove each filter.

Start with three explicit supported forms: “notes tagged TAG,” “notes from MONTH YEAR,” and “notes from last month.” Define how supported filters can combine with residual search terms. Unknown or ambiguous syntax must remain an ordinary text query or request a clarification; it must not quietly become a broad query with discarded words.

Define a typed SearchRequest with residualText, tags, optional startInclusive/endExclusive instants, dateField, and interpretation status. Validate values before passing them to the repository. Build parameterized SQL from this object inside application code. Do not execute model-generated SQL, paths, or expressions.

Choose which timestamp “from” refers to, such as the note's declared created_at, and make it visible. Files without trusted creation metadata are not automatically assigned their scan/import date. Show a warning when that prevents a date match. A mention of California in the body is a text match, not verified GPS metadata.

Inject a clock and user timezone. Calendar-month boundaries must be computed in that timezone and then converted to instants. Test with a fixed anchor of 2026-10-17 in a configured test timezone: “last month” means September 1 inclusive through October 1 exclusive locally. This is a test fixture, not a production hardcoded date. Handle year boundaries and daylight-saving transitions through a maintained date/time API already compatible with the project.

For “last January,” dates without years, conflicting filters, or unsupported language, explain the ambiguity or show the exact interpretation for confirmation. Do not infer the user's locale from a device path or a training example.

Write deterministic tests for supported forms, mixed keywords, unsupported phrasing, quoted text, malformed tags, missing dates, boundary timestamps, timezone differences, and cancellation of a suggested interpretation. Confirm that removing a filter restores the original query behavior.

If I later request model-based interpretation, keep it behind the same typed schema and validation, with a reviewed local/remote inference choice. Limit the model to producing a proposed filter object. No tool use and no hidden network calls. Show uncertain output rather than claiming its interpretation is authoritative.

Stop when the agreed grammar and editable filter UI work. Do not add embeddings, a chat interface, calendar access, location collection, or generation unless separately requested.

## Completion evidence

Show original text, parsed fields, the exact date interval/timezone, and the notes returned for the fixed synthetic fixture. An unsupported sentence must not look like a successfully understood query.
