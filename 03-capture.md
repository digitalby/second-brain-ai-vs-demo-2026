Recommendation: Run after the Store prompt has established a working save/read adapter. A fake store is useful for UI tests but cannot satisfy this prompt's completion gate.

## Prompt

Read CLAUDE.md, docs/decisions.md, and existing Capture and Store code. Implement only the approved capture route. Default Android scope is a plain text editor plus an ACTION_SEND receiver for text/plain. Desktop scope is the editor. Reuse the existing VaultStore and route every save through the same capture controller.

The user can create a draft, edit it, save it explicitly, see an honest saving/saved/error state, and open the resulting note. Define empty/whitespace-only behavior and an input-size limit in the plan before implementing. Prefer rejecting an empty note with a clear message and preserving oversized input while explaining the limit.

On Android, validate intent action, MIME type, and EXTRA_TEXT without assuming a non-null String. Receive initial and newly delivered intents correctly. Show incoming text as a draft rather than saving it immediately. A save button is disabled while a submission is in flight. Preserve a draft identifier through recreation so rotating or recreating the activity cannot replay a completed save. Identical text intentionally submitted as a new draft may create a new note; do not deduplicate all content globally.

Use a state holder with StateFlow and a lifecycle-appropriate coroutine scope. Dispatch blocking adapter work away from the UI. Emit Saved only after VaultStore confirms the write. If the file saved but indexing failed, report Saved with search pending, not a false save failure. Preserve the draft on a storage error and allow a safe retry. Decide and document whether unsaved drafts survive process death; do not imply this from rotation support alone.

Keep platform-specific intent handling outside commonMain. Use a minimal exported share component with the necessary filter, while unrelated components remain unexported. Do not request microphone, location, Bluetooth, contacts, broad storage, or network permissions for typed capture.

Write behavior tests for an empty draft, successful save, rejected write, retry, double tap, cancelled share, missing payload, and recreated activity. Include an integration check that an actual Markdown file contains the exact saved text, including emoji and line breaks. On Android, exercise the share flow from another app on the selected emulator/device; if unavailable, label that check not run.

Stop after one complete capture-to-file route works. Do not add widgets, tiles, PROCESS_TEXT, voice, attachments, or lock screen features unless already selected in the plan. If requested later, Glance widgets and TileService tiles need separate platform work. Report files changed, checks run, checks not run, and remaining draft-lifecycle limitations.

## Completion evidence

A synthetic thought shared or typed once produces one readable note; an injected storage failure preserves the draft; an actual restart reopens a committed note. Report device/OS and actual results. The reusable prompt itself is not evidence that these checks passed.
