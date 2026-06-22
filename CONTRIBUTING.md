# Contributing to OxideAV

Thank you for your interest in OxideAV. We welcome bug reports, fixes, and
improvements. Because of how this project is built, contributions have a few
requirements that are stricter than a typical open-source project — please read
this before opening a pull request.

## The clean-room rule (the most important one)

Every codec, container, and format implementation in OxideAV is written
**clean-room from public specifications only**. This is a hard legal boundary,
not a style preference.

**A contribution must not be derived from — or written while consulting — the
source code of any other implementation.** That includes (non-exhaustively)
FFmpeg / libav\*, the JM / HM / reference decoders, libFLAC, libvpx, libwebp,
OpenJPEG, x264/x265, or any other codec library or reference implementation —
**even if you wrote the resulting code yourself.** Reading reference source and
then re-typing the logic produces a derivative work and is not acceptable.

What **is** fine:
- Deriving from the published specification (ITU-T, ISO/IEC, IETF RFC, ATSC,
  ETSI, SMPTE, vendor format specs, etc.).
- Running another implementation as a **black-box validator** (e.g. invoking the
  `ffmpeg` CLI to encode a test vector or to compare decoded output). Running the
  binary is fine; reading its source is not.
- **Numeric data tables** dictated by a specification (codebooks, quantisation
  matrices, init tables). A spec-defined table is data, not authorship, and may
  be transcribed from any source.

If you are unsure whether something is on the right side of this line, ask in the
issue before writing code.

## Licensing & originality

OxideAV is licensed under the **MIT License**. By contributing you confirm that:

1. The contribution is your own original work and you have the right to submit it.
2. It is **not** derived from another implementation's source (see above).
3. It carries **no** third-party licensing obligations (no GPL/LGPL/other-licensed
   code is copied or adapted).
4. You agree to license your contribution under the **MIT License** as part of
   OxideAV.

These points are restated as a checklist in the pull-request template; please
confirm each one. First-time contributors will also be asked to sign the project
Contributor License Agreement (handled automatically on your first PR).

## Practical notes

- Keep changes scoped to a single crate where possible.
- Do not commit specification PDFs or third-party media as test fixtures —
  generate synthetic, clearly-licensed fixtures instead.
- Run `cargo fmt` and `cargo clippy --all-targets -- -D warnings` before pushing.
- Don't bump `[package] version` — releases are automated.

We genuinely appreciate contributions — the clean-room rule exists so the whole
project stays freely usable, and a little care up front keeps your work mergeable.
