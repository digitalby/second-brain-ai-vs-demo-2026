# Store implementation

Recommended: Run after setup and before Capture. Start with a new, disposable local vault. Supporting every existing Obsidian convention or every document provider is outside this iteration.

## Prompt

Read CLAUDE.md, docs/decisions.md, and existing code. Implement VaultStore for the approved platform, preserving Markdown files as the source of truth. Propose the small API from actual caller needs: list, read, create, update, and rescan. Defer an in-app deletion feature unless explicitly requested.

For app-created notes, document a minimal front matter containing schema_version, id, title, created_at, and updated_at. Use stable random IDs, UTC timestamps, safe unique filenames, and correct escaping of metadata values. Inject the clock/ID source for tests. Keep the body as UTF-8 Markdown with its line breaks preserved. Define a supported metadata subset or select a maintained parser with verified target support; do not pretend a few regular expressions implement general YAML.

Existing Markdown without app metadata must remain readable. Never stamp IDs or normalize the entire vault automatically. Keep an index-side identity for imports. Preserve unknown metadata byte-for-byte when possible; if a front-matter construct cannot be round-tripped safely, allow reading and explain why editing is unsupported. Detect duplicate declared IDs and report ambiguity instead of overwriting a different note.

Implement the platform adapter. For desktop, restrict canonicalized paths to the chosen folder, handle unsafe names/symlinks, and use a verified filesystem library such as Okio. For Android, launch ACTION_OPEN_DOCUMENT_TREE, persist the permission flags actually granted, and use ContentResolver/DocumentsContract with DocumentFile if useful. Document URIs are opaque handles, not java.io.File paths. Test a local provider selected by the user. Handle permission revocation without replacing the vault or losing drafts.

First design the write and recovery protocol for the selected adapter. On a local filesystem, use a temporary sibling and supported replacement semantics, with required flush/durability steps documented. For a document provider, probe supported create/rename/write behavior. Do not claim atomic replacement based on a desktop test. During edits, retain a recoverable previous version and sufficient state to distinguish committed notes from incomplete writes after restart. Do not delete the old version before a replacement is verified. If reliable updates cannot be demonstrated, disable editing for that adapter and propose create-only capture or a desktop-first scope change. Do not secretly move the source of truth into a database.

Serialize writes and compare the current file's content hash/version with what the editor read. Refuse an observed conflict or preserve both versions. State the residual race when the provider lacks compare-and-swap; simultaneous writers are unsupported.

Return storage success independently of derived-index status. Publish a change notification after a successful write. Rescan on foreground entry and manual refresh, including externally changed/new/deleted files. Treat inaccessible files as unknown, not deleted. Do not build background synchronization.

Add tests for Unicode, filename collision, malformed/unknown front matter, imported plain Markdown, duplicate IDs, read-only access, short/failed writes, interrupted replacement recovery, permission loss, and external edit conflict. Use real adapter integration tests as well as fakes. Backups/recovery artifacts must not appear as ordinary notes or search results. Only remove disposable fixtures created by the tests.

Stop after the selected adapter supports the agreed read/create/update/recovery behavior. Document exact guarantees and limitations; do not claim universal atomicity or power-loss durability from process-restart tests alone.

## Completion evidence

Open a saved file in an independent editor. Restart the application and read it unchanged. Interrupt a replacement at controlled failure points and show the recoverable old/new content. On Android, revoke the folder permission and verify that the app explains the problem without silently overwriting anything.
