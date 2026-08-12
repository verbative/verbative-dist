# Third-party license texts

Full license texts for the third-party components distributed in this repository's binary
releases. Organized by where the component lives in the binaries:

- **`runtime/`** — components not carried inside the binaries themselves:
  - `whisper.cpp-MIT.txt` — whisper.cpp (MIT), compiled into `verbative-whisper-cli` /
    `verbative-whisper-server`.
  - `llama.cpp-MIT.txt` — llama.cpp (MIT), compiled into `verbative-llm` (the local
    memory extractor LLM runtime).
  - `CPython-3.11-PSF.txt` — the CPython 3.11 runtime (PSF License) embedded by PyInstaller
    in all three Python binaries.
- **`models/`** — pronunciation data baked into `verbative-tts`:
  - `CMUdict-BSD-2-Clause.txt` — the CMU Pronouncing Dictionary (BSD-2-Clause).
  - `g2p-en-Apache-2.0.txt` — the g2p-en model (Apache-2.0).
  - The **local memory models** (Qwen3-Embedding-0.6B, Qwen3-4B, the RAM-tiered Qwen3
    reader models, and bge-reranker-v2-m3 —
    all Apache-2.0) are downloaded at runtime from their official Hugging Face repos; each
    ships its own `LICENSE` in-repo, so the Apache-2.0 license travels with the download and
    is not re-hosted here. The same applies to the other runtime-downloaded models — the
    Whisper GGML models and Silero VAD (both MIT) and the Kokoro-82M voice weights
    (Apache-2.0): fetched at setup from their official repos, license traveling with the
    download.
- **`python-packages/`** — the license text for each bundled Python dependency, mirrored
  from its packaging metadata (`*.dist-info/`) inside the binaries.
- **`windows/`** — curated texts for the components only the Windows bundles carry
  (PortAudio/sounddevice, psutil, scipy, scikit-learn, OpenBLAS, onnxruntime-directml);
  these ship without usable packaging metadata in the frozen bundles, so their texts are
  maintained here.

**No GPL/AGPL code ships.** The `phonemizer` module present in the bundle is Verbative's
GPL-free no-op stub, not the upstream GPL package.

See [`../THIRD_PARTY_LICENSES.md`](../THIRD_PARTY_LICENSES.md) for the full component
inventory and each component's license.
