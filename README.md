# Verbative — binary distribution

This repository hosts the **release binaries** for the
[Verbative](https://verbative.de) VS Code extension. It contains **no source code** — it
exists only to serve the on-device engine binaries (speech-to-text, text-to-speech, the
wake daemon, and the hook runner) that the extension downloads and verifies at install
time.

- **Get Verbative:** install the extension from the
  [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=verbative.verbative),
  or visit [verbative.de](https://verbative.de).
- **Binaries** are published under [Releases](../../releases) — one tag per component, per
  platform — and are integrity-checked (SHA-256) by the extension before use. End users
  never fetch them manually.
- **Source code is not public.** Verbative is proprietary software; see [`LICENSE`](./LICENSE).
- **Third-party components** bundled inside the binaries, with their licenses, are listed
  in [`THIRD_PARTY_LICENSES.md`](./THIRD_PARTY_LICENSES.md).

Not affiliated with Anthropic. "Claude" and "Claude Code" are trademarks of Anthropic, PBC,
used here nominatively.

© 2026 Florian Cramer (Cramer Digital). All rights reserved. · info@verbative.de ·
[Impressum](https://verbative.de/legal/impressum)
