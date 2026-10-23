# AGENTS.md

Repository-specific instructions for this fork.

- Keep inference free of SilentCipher initialization and post-processing. The shared implementation is `irodori_tts/inference_runtime.py`; `infer.py` and both Gradio apps use it.
- Preserve the DACVAE mono decoder passthrough in `irodori_tts/codec.py`. See `NOTES.md` for its role and the distinction from the removed SilentCipher stage.
- Keep `pyproject.toml`, `requirements.txt`, and `uv.lock` consistent when changing dependencies. Preserve unrelated pinned versions and minimize lockfile churn.
- Preserve upstream README content; add concise fork-specific notes. Record behavior changes in `CHANGELOG.md` and implementation rationale or verification limits in `NOTES.md`.
- For inference-runtime changes, run `ruff check irodori_tts/inference_runtime.py`. For dependency changes, also run `uv lock --check` with a supported, available Python interpreter.
