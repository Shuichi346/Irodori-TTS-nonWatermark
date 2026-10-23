# Notes

## 2026-10-23 — Inference without SilentCipher

`InferenceRuntime.__init__` previously constructed a SilentCipher model, and `synthesize` encoded every decoded candidate when that model was available. Removing both paths avoids model-loading overhead and the per-generation watermark stage across `infer.py`, `gradio_app.py`, and `gradio_app_voicedesign.py`.

The runtime now returns the codec output after its existing optional tail trimming. This change prevents adding a SilentCipher watermark to newly generated audio; it does not process existing audio files to remove watermarks.

The separate DACVAE decoder passthrough in `irodori_tts/codec.py` remains necessary for the checkpoint's mono decoding path. It already bypasses the codec watermark branch and must not be removed as unused watermark code.

The dependency update retained every remaining pinned package version and the existing lockfile format. Only SilentCipher and four packages no longer needed by the dependency graph were removed.

Validation used Python 3.12.14 and PyTorch 2.10.0 on CPU. A temporary smoke harness executed the actual runtime with model, tokenizer, sampler, reference-loading, and codec boundaries substituted. All 16 combinations of RF/MeanFlow, sequential/batch decoding, tail trimming on/off, and captions on/off passed. Checks covered two audio candidates, exact expected samples, output metadata, and the absence of watermark imports, messages, and timing stages. Ruff and `uv lock --check --offline` also passed.

No model checkpoints were downloaded. Real speech generation, audio quality, MPS execution, and wall-clock performance improvements were not measured.
