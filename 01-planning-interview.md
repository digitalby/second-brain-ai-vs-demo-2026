# Planning interview

## Prompt

Read the project's CLAUDE.md and existing source, build configuration, and tests. If the repository is empty, say so. Help me specify a useful personal Second Brain using Capture → Store → Surface.

Interview me before proposing an architecture. Ask up to three related questions per round, then wait for my answers. Reuse information I have already provided. Explain tradeoffs briefly and recommend a default when helpful. Do not scaffold, edit files, install software, or run commands with side effects during this planning pass.

Resolve these decisions, in a sensible order:

1. Which thought or material do I keep losing, where am I when that happens, and how will I want to retrieve it? Ask for a concrete example and a measurable reduction in capture friction.
2. Which ONE device and operating system must work first? Is the SDK/toolchain already installed? Android is the phone-first recommendation. Desktop is an easier first path for direct local-folder access. iOS and web require separate acceptance work.
3. Do I want typed capture, shared text, voice, or something else? Recommend one primary route plus an Android share target only if relevant. Defer voice unless it is the essential use case, in which case reduce other scope.
4. Is there an existing vault, which languages appear in it, and how is it organized? Ask for synthetic examples, not private files. Decide folder/provider, imported-file handling, metadata conventions, edit-conflict behavior, and backup expectations.
5. What must work by the end of this iteration? Recommend create/read/basic edit plus lexical retrieval, using a disposable vault. Separate essentials from graph, embeddings, natural-language filters, RAG, and extra platforms.
6. Can any app content leave the device? Default to no. Distinguish app inference from the online coding-agent session. Resolve this before selecting any remote service.

When the consequential choices are resolved, present a concise proposal with: the user story; approved inputs and assumptions; one target; a Capture/Store/Surface responsibility map; concrete libraries with platform limitations; file format and index ownership; in/out scope; implementation order; acceptance checks; and stop conditions. Highlight unresolved decisions. Do not bury extra infrastructure in “setup.”

Include this example acceptance scenario, adapted to my answers: share or type a synthetic thought, save it once, inspect the Markdown externally, restart, retrieve it offline, rebuild the derived index from the files, and retrieve it again. Include a failed-save recovery case.

Ask for approval of this specific plan. Once approved and editing is enabled, the next implementation step should save the agreement to docs/decisions.md. Do not interpret silence as approval.

## Expected result

A reviewable plan and explicit decisions, with no claim that the app exists. The interview is complete when the first target, storage model, capture route, retrieval gate, and exclusions are settled. Move to `02-toolchain-setup.md` after approval.
