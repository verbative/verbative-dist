# Third-party components & licenses

The binaries distributed in this repository (the `verbative-whisper-cli`,
`verbative-whisper-server`, `verbative-daemon`, `verbative-hooks`, `verbative-tts`, and
`verbative-llm` and `verbative-parakeet-ane` release assets, plus the `verbative-memory-core`
bundle — the same engine core packaged for the `verbative-memory` pip/npm launchers), built
for macOS (Apple silicon) and Windows (x64), and the Verbative Voice apps (the `voice-*`
releases, [below](#verbative-voice-the-voice--releases)) bundle the third-party
open-source components listed below, each under its own license. Verbative ships **no copyleft (GPL/AGPL) code**. Each pip-installed Python
dependency's license text ships inside the binaries (under `_internal/*.dist-info/`,
including the LGPL text for num2words). The components whose texts are **not** embedded —
whisper.cpp, llama.cpp, and the CPython runtime — have their full texts in the
[`LICENSES/`](./LICENSES/) folder of this repository, which also mirrors each bundled
Python package's license text and the baked-in model licenses.

## Speech-to-text

- **whisper.cpp** (ggerganov) — MIT. The official `whisper-cli` / `whisper-server` tools,
  compiled from source (v1.9.1) and shipped as `verbative-whisper-cli` /
  `verbative-whisper-server`.
- **Whisper GGML models** (`small.en` + `large-v3-turbo` by default; others downloaded on
  demand, from huggingface.co/ggerganov/whisper.cpp) — MIT (OpenAI Whisper weights, GGML
  conversion).
- **Silero VAD v5** (snakers4/silero-vad, ggml conversion from
  huggingface.co/ggml-org/whisper-vad) — MIT. Voice-activity model run natively by
  whisper.cpp (`--vad`) so non-speech audio is never transcribed; downloaded beside
  the dictation models (~1 MB).

- **Parakeet TDT 0.6B v3** (NVIDIA, huggingface.co/nvidia/parakeet-tdt-0.6b-v3) —
  **CC-BY-4.0**. The extension's default dictation model on a Mac whose language it speaks,
  and Verbative Voice's second speech engine; downloaded at setup, not hosted here.
  Attribution: "Parakeet TDT 0.6B v3" by NVIDIA, licensed under CC-BY-4.0
  (https://creativecommons.org/licenses/by/4.0/). On macOS in FluidInference's Core ML
  conversion (huggingface.co/FluidInference/parakeet-tdt-0.6b-v3-coreml), on Windows
  (Verbative Voice) in ggml-org's GGUF conversion (huggingface.co/ggml-org/parakeet-GGUF),
  each used unmodified from there.
- **`verbative-parakeet-ane`** — the macOS helper that runs Parakeet on the Neural Engine
  with Core ML (Swift, Verbative's own code), shipped as its own release asset for the
  extension and inside Verbative Voice. Compiled into it, full texts in `LICENSES/runtime/`:
  - **FluidAudio** 0.18.4 (FluidInference, github.com/FluidInference/FluidAudio) —
    Apache-2.0, with patches of ours.
  - **fastcluster** (© 2011 Daniel Müllner, © Google Inc.) — BSD-2-Clause, part of
    FluidAudio's sources.

  Verbative Voice's Windows helper, `verbative-parakeet.exe`, is built on whisper.cpp's ggml
  (MIT, above) with Verbative's own code.

## Text-to-speech

On Apple Silicon the default engine runs Kokoro on **MLX (Apple GPU)**; on Windows the ONNX
path runs on **DirectML (GPU)** where available; ONNX-CPU is the universal fallback. All
paths use the permissive components below.

- **Kokoro / Kokoro-82M** (hexgrad) — Apache-2.0. The ONNX path uses `kokoro-v1.0.onnx`
  (Apache-2.0); the MLX path downloads `mlx-community/Kokoro-82M-bf16` (Apache-2.0) at setup.
- **MLX** + **mlx-audio** (Apple) — MIT. The Apple-GPU inference path; we call the model
  directly and reuse our own GPL-free G2P, bypassing mlx-audio's espeak pipeline.
- **onnxruntime** (Microsoft) — MIT. The cross-platform ONNX inference engine (the Windows
  bundle ships the `onnxruntime-directml` variant — same MIT license).
- **huggingface_hub** — Apache-2.0. Used only to fetch the public MLX weights at setup.
- **misaki** (English G2P) — Apache-2.0. Its `espeak.py` is replaced with a GPL-free no-op
  stub so the GPL espeak fallback is never used.
- **kokoro-onnx** (thewh1teagle) — MIT. Python wrapper for the ONNX inference path.
- **CMU Pronouncing Dictionary** (`cmudict.dict`, Carnegie Mellon University) — BSD-2-Clause.
  Bundled and used by our permissive, GPL-free out-of-vocabulary G2P fallback.
- **g2p-en** (Kyubyong Park) — Apache-2.0. We bundle only its trained numpy model
  (`g2p_checkpoint.npz`) and port its character-level seq2seq inference; no nltk, no network.
- **num2words** — **LGPL-2.1**. Used to expand numbers to words. Commercial distribution is
  permitted under the LGPL: the package is used unmodified, its full LGPL-2.1 license text
  ships in the bundle, and its source is publicly available at
  https://github.com/savoirfairelinux/num2words. It is dynamically loaded and can be
  replaced by the user.
- **spaCy** + `en_core_web_sm` model — MIT.

_(`soundfile` / libsndfile is **not** shipped: audio is written with the stdlib `wave`
module and the tts build excludes the libsndfile data payload, so no native LGPL library is
bundled. `num2words` remains the only LGPL component.)_

### Transitive runtime dependencies (all permissive)
Pulled in by the above and bundled by PyInstaller; all OSI-approved permissive licenses
(license texts retained per package under `*.dist-info/`):

- **MIT:** catalogue, cymem, murmurhash, preshed, srsly, thinc, wasabi, weasel, spacy-legacy,
  spacy-loggers, smart_open, setuptools, attrs, pydantic, pydantic-core, charset-normalizer,
  PyYAML, mlx, mlx-metal, mlx-audio, wheel
- **BSD-3-Clause:** blis, numpy, MarkupSafe, click — plus, in the Windows bundles: scipy,
  scikit-learn, and the OpenBLAS libraries embedded in the numpy/scipy wheels
  (`numpy.libs` / `scipy.libs`)
- **BSD-2-Clause:** wrapt
- **MPL-2.0:** **tqdm** (MPL-2.0 AND MIT) and **certifi** — weak/file-level copyleft; permitted
  in proprietary distribution; unmodified, license texts retained in the bundle.
- **Apache-2.0:** huggingface_hub, hf_xet, importlib_metadata, misaki, regex

## Local memory (semantic retrieval + capture)

The channel shared-memory system runs fully on-device. Retrieval and capture work with
no models (lexical mode); the models below add semantic ranking and local automatic
capture. **All models are official Qwen3 GGUFs (Apache-2.0)**, downloaded directly from
Qwen's own Hugging Face repositories and run through the bundled llama.cpp — no ONNX, no
self-conversion, no third-party mirrors. The extractor is pinned in code to a
single hardcoded permissive model, verified against a pinned sha256 — **no Llama, Gemma,
or non-free model sizes are ever shipped or fetchable**.

- **llama.cpp** (ggml-org) — MIT. The official `llama-server`, compiled from source and
  distributed as the `verbative-llm` release asset; runs all three memory models
  (embedding, reranking, extraction) in-process over a local HTTP endpoint.
- **Qwen3-Embedding-0.6B (GGUF)** (Alibaba) — **Apache-2.0**. The multilingual embedding
  model (semantic retrieval); downloaded at setup from the official Qwen repo.
- **Qwen3-4B (GGUF)** (Alibaba) — **Apache-2.0**. The local memory extractor LLM (auto
  capture); downloaded from the official Qwen repo and verified against a pinned sha256.
- **bge-reranker-v2-m3 (GGUF)** (BAAI, via the gpustack GGUF conversion) — **Apache-2.0**.
  The retrieval reranker (llama.cpp-native cross-encoder); downloaded at setup from Hugging
  Face. Each model's license travels with the download from its official repo.
- **Qwen3 reader models (GGUF)** (Alibaba) — **Apache-2.0**. The optional on-device reader
  model (it powers `ask`), tiered to the machine's RAM (4B/8B/32B) and allowlisted in code
  to the official Qwen Hugging Face repos; downloaded on first use or via
  `verbative-memory setup`.

## Python binary runtime (daemon, hooks, tts)

`verbative-daemon`, `verbative-hooks`, and `verbative-tts` are PyInstaller bundles (the PyInstaller
bootloader they embed is GPL-2.0 **with the bootloader exception**, which expressly permits
bundling into proprietary programs). In addition to our own code, each embeds the Python
runtime; the daemon additionally bundles the capture libraries:

- **CPython 3.11 standard library** (`libpython3.11`, in all three) — Python Software
  Foundation License (PSF). Full text in `LICENSES/`.
- **numpy** — BSD-3-Clause.
- **PyObjC** — MIT (macOS: the daemon's AVFoundation audio capture).
- **sounddevice + PortAudio** — MIT (Windows: microphone capture and audio playback). The
  PortAudio DLL variant carrying the Steinberg-licensed ASIO backend is **stripped at build
  time** — only the plain-MIT DLL ships.
- **psutil** — BSD-3-Clause (Windows: process-tree and environment walks in the daemon and
  hooks).

## Verbative Voice (the `voice-*` releases)

The Verbative Voice downloads are distributed here too: the macOS dmg and update zip, the
Windows installer, and the Windows bootstrapper (`voice-setup`). Each app carries its own
complete list, `THIRD_PARTY_LICENSES.md`, inside the app (macOS:
`Verbative Voice.app/Contents/Resources/`; Windows: the install folder's `resources\`), next
to Electron's and Chromium's notices. In short:

- **Electron** (Chromium, Node.js) — MIT, with Chromium's permissive component licenses;
  Electron's `ffmpeg` library — LGPL-2.1, unmodified, without the GPL codecs.
- **electron-updater** and its dependencies — MIT / ISC / BlueOak-1.0.0 (the Windows app's
  updates).
- **whisper.cpp** and **llama.cpp** — MIT, the same binaries as above; **Parakeet's
  helpers** — above.
- The frozen **Python sidecar**: CPython (PSF), numpy (BSD-3-Clause), PyObjC (MIT, macOS),
  sounddevice + PortAudio and PyWinRT (MIT, Windows), ONNX Runtime (MIT), sentencepiece
  (Apache-2.0), protobuf (BSD-3-Clause). Their texts are in `LICENSES/`, collected from the
  built sidecar.
- Models downloaded at first run, not hosted here except the paragraph model: the Whisper
  GGML models and Silero VAD (MIT), Parakeet TDT 0.6B v3 (CC-BY-4.0, above), and AI
  Polish's Spokenly Cleanup 1 (Apache-2.0, a fine-tune of Qwen3-0.6B-Base) and Segment any
  Text sat-12l (MIT; its 8-bit ONNX, quantized by Verbative, is the `voice-paragraphs-1`
  release here) with XLM-RoBERTa's sentencepiece model (MIT).

## No GPL code shipped (GPL deps replaced with GPL-free stubs)

- **phonemizer / phonemizer-fork** — GPL-3.0 — **not shipped**. Replaced at build time with a
  GPL-free no-op stub: an importable `phonemizer` placeholder whose `phonemize()` raises, so
  no GPL code is present or executed.
- **espeak-ng / espeakng-loader** — GPL-3.0 — **not shipped**. Likewise replaced with a
  GPL-free stub.

These are pulled in transitively by Kokoro only for an out-of-dictionary phonemizer fallback
Verbative does not use; out-of-vocabulary words are instead phonemized by our permissive
CMUdict + g2p-en fallback. The build swaps the GPL packages for the stubs after every Kokoro
deps install, leaving the shipped binaries GPL-free (verified: the only `phonemizer` in the
bundle is the stub).

---

_This list covers the third-party runtime components bundled in the distributed binaries
(native binaries, Python bundles, and on-device models) on both platforms. No GPL/AGPL or
non-commercial licenses are shipped. The only LGPL/MPL components (num2words — LGPL-2.1;
tqdm and certifi — MPL-2.0) are included unmodified, with their full license texts retained
in the bundle, as those licenses permit for commercial distribution._
