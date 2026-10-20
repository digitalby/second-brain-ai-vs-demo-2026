# Traditional full-text search

Recommended: Run after Store and Capture. This completes the baseline Surface layer. It requires no embedding model or network service.

## Prompt

Read CLAUDE.md, docs/decisions.md, VaultStore, and current search code. Implement SearchIndex with AndroidX SQLite's BundledSQLiteDriver on the selected target. Verify the published dependency/source-set configuration. Prove FTS5 is available by creating and querying a disposable FTS5 table using the same driver packaged in the app. If it fails, diagnose the build instead of silently substituting a hosted service or pretending LIKE is FTS5.

Use an app-private derived database separate from the Markdown vault. Start with a catalog of note identity, source locator, title, content hash, and index revision, plus a contentful FTS5 table over title/body. Keep catalog/FTS changes in one SQLite transaction and serialize access appropriately. The database is allowed to duplicate text because it is disposable. Avoid external-content triggers and vector extensions in this first implementation.

After a successful file write, upsert the current text. An index failure does not undo or misreport a saved note. Show a stale-search state and offer repair. Refresh externally edited/deleted notes through the Store rescan. Resolve result IDs to current sources and handle moved/missing notes gracefully.

Define the query grammar explicitly: whitespace-separated ordinary terms use AND; double quotes request an adjacent phrase; unmatched quotes are treated as literal text or produce a friendly validation message, with the chosen behavior tested. Empty input returns recent notes with a clear label. Advanced raw FTS operators are out of scope. Escape tokens for the FTS query language and bind the resulting expression as a SQL parameter. Reject or normalize unsupported punctuation safely. SQL parameter binding alone does not validate MATCH syntax.

Return ranked results with title, a short excerpt, and a source action. Prefer title hits through documented weighting. Order FTS5 bm25 ascending and add a stable tie-break. Render excerpts as plain text/spans rather than trusting HTML from note content. Use cancellation/debouncing so an older slow query cannot overwrite newer results.

Provide Rebuild Index: scan supported Markdown files, populate a new complete index, and switch only after success. Opening an inaccessible vault must not wipe the previous catalog. Distinguish confirmed deletions from read errors. Rebuilding FTS from stale database rows is not a rebuild from the vault.

Create a small deterministic fixture vault with a charger packing note, a note containing a quoted phrase, an unrelated recipe, Unicode text, duplicate titles, and an externally edited note. Verify expected lexical results, phrase behavior, literal punctuation, empty input, ranking direction, edits/deletions, interrupted rebuild, and rebuild after removing only the derived index. Record tokenizer/language limitations; English stemming is not a universal language solution.

Run the full capture-save-restart-search-open loop offline on the selected application target. Report the exact environment and failures. Stop here unless another extension has been explicitly requested.

## Completion evidence

The saved file remains unchanged when the derived database is discarded and rebuilt. Queries find the same expected notes before and after a successful rebuild. Search failures cannot delete source content.

Technical reference: [SQLite FTS5](https://www.sqlite.org/fts5.html). Query grammar, catalog design, rebuild strategy, and acceptance fixtures above are proposed project choices.
