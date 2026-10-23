# Changelog

## 2026-10-23

### Added

- Added macOS Finder metadata, AppleDouble resource forks, and filesystem service artifacts to `.gitignore`.

### Removed

- Removed SilentCipher initialization and watermark encoding from the shared inference runtime used by the CLI and both Gradio apps.
- Removed the SilentCipher wrapper, watermark status logging, watermark stage timing, and missing-watermark warnings.
- Removed SilentCipher from both dependency manifests and the lockfile, along with its unused dependencies `blinker`, `flask`, `itsdangerous`, and `pyaml`.

### Changed

- Returned generated audio directly after codec decoding and optional tail trimming, without an additional flag.
- Added a short fork-specific README section while preserving the upstream documentation.
