# Notes

## 2026-11-11 — Upstream inference and Python 3.13

The upstream runtime routes single requests through `synthesize_batch`. `_finish_request` returns the decoded, optionally trimmed audio directly for each request. Keep this path free of SilentCipher and preserve the DACVAE mono passthrough described below.

SentencePiece 0.1.99 lacks CPython 3.13 wheels. Both dependency manifests require the 0.2 series, and the lockfile no longer includes dependency branches for Python below 3.13.

On macOS with Python 3.13.15, the locked CPU extra installed cleanly. MeanFlow CLI synthesis and two-request batching passed on CPU. The batch produced finite mono waveforms of the expected lengths and reported no watermark stage. The optional two-pass length check resynthesized audio with the RF v4.1-Small checkpoint on CPU and on MPS with the CPU codec. Ruff and the lockfile consistency check passed.

## 2026-10-23 — Inference without SilentCipher

`InferenceRuntime.__init__` previously constructed a SilentCipher model, and `synthesize` encoded every decoded candidate when that model was available. Removing both paths avoids model-loading overhead and the per-generation watermark stage across `infer.py`, `gradio_app.py`, and `gradio_app_voicedesign.py`.

The runtime now returns the codec output after its existing optional tail trimming. This change prevents adding a SilentCipher watermark to newly generated audio; it does not process existing audio files to remove watermarks.

The separate DACVAE decoder passthrough in `irodori_tts/codec.py` remains necessary for the checkpoint's mono decoding path. It already bypasses the codec watermark branch and must not be removed as unused watermark code.

The dependency update retained every remaining pinned package version and the existing lockfile format. Only SilentCipher and four packages no longer needed by the dependency graph were removed.

Validation used Python 3.12.14 and PyTorch 2.10.0 on CPU. A temporary smoke harness executed the actual runtime with model, tokenizer, sampler, reference-loading, and codec boundaries substituted. All 16 combinations of RF/MeanFlow, sequential/batch decoding, tail trimming on/off, and captions on/off passed. Checks covered two audio candidates, exact expected samples, output metadata, and the absence of watermark imports, messages, and timing stages. Ruff and `uv lock --check --offline` also passed.

No model checkpoints were downloaded. Real speech generation, audio quality, MPS execution, and wall-clock performance improvements were not measured.
