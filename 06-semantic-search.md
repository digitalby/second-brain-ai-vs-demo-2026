# Optional semantic search / NLP embeddings

Advanced extension. The lexical app must already work. This adds retrieval, not generated answers. Applying this prompt authorizes planning the extension; confirm the model download, device budget, and any new target before adding a runtime.

## Prompt

Read CLAUDE.md, docs/decisions.md, and the existing lexical search. Propose an optional EmbeddingProvider behind a shared interface while keeping platform runtimes out of commonMain. Preserve the fully working keyword path. Record the target, languages, model/license, download size, expected memory budget, and an agreed latency target before implementation.

For an English test corpus, evaluate sentence-transformers/all-MiniLM-L6-v2 as a concrete candidate. Its documented output is 384 dimensions and its default limit is 256 word pieces. Do not treat it as a multilingual guarantee. Pin the model revision, export checksum, tokenizer assets, maximum input length, pooling method, and normalization. Confirm these properties against the chosen export, not just the model family name.

Use ONNX Runtime's platform package when appropriate: com.microsoft.onnxruntime:onnxruntime-android for Android, or com.microsoft.onnxruntime:onnxruntime for a separately approved JVM desktop target. Verify CPU architecture/native library support for the actual machine. iOS requires its own native binding and integration. ONNX Runtime does not supply the complete text-tokenization pipeline. Select and verify a tokenizer implementation for the actual target before promising implementation; if no workable one is established, stop the spike with an explicit blocker, leaving keyword search intact.

Use the exact tokenizer vocabulary/configuration and special-token rules. Validate input tensors and attention masks. If the ONNX export emits token embeddings, apply attention-mask-aware mean pooling and normalization as documented. If it already emits final sentence embeddings, do not pool again. Compare a few golden inputs against a trusted reference encoder with stated tolerances. Record how the reference values were generated.

Chunk by Markdown sections, splitting long sections into overlapping tokenizer-bounded windows. Start with a budget around 200 word pieces including title/heading and overlap around 32, adjusted so special tokens plus content fit the model limit. Record boundaries, note ID, source hash, section, and offsets. Never silently truncate an entire long note into one vector.

Cache embeddings by source/chunk hash plus model, tokenizer, pooling, and chunker revision. Re-embed only changed chunks; remove confirmed deleted chunks. A configuration change invalidates the relevant cache. Store vectors as derived data in the existing local index with dimensions and revision metadata. On a small vault, perform an exact cosine scan over normalized vectors. Handle zero/invalid vectors. Do not add a vector service or approximate-nearest-neighbor library until measurements justify it.

Index in bounded batches off the UI thread, expose progress/cancellation, and continue lexical search while indexing. Missing/corrupt model files, offline operation before provisioning, or an inference error must fall back to keyword search with an explicit status. Model provisioning can use the network with consent; query inference should use provisioned local assets only.

Create at least twelve synthetic, labeled query-to-note examples, including paraphrases, exact identifiers, unrelated questions, and the user's languages. Compare lexical and semantic recall@5, inspect false positives, and measure warm/cold latency and peak memory on the actual target. Set pass/fail thresholds before inspecting results. Mock vectors test arithmetic and invalidation, not semantic quality. If semantic search underperforms, report that honestly and leave it optional/off.

Stop after a measured retrieval experiment with source links. Do not add RAG, automatic tagging, or natural-language query rewriting.

References: [model card](https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2), [ONNX Runtime packages](https://onnxruntime.ai/docs/install/), [JVM binding](https://onnxruntime.ai/docs/get-started/with-java.html).
