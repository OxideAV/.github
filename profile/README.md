# OxideAV

**Pure-Rust media transcoding and streaming.** No C libraries, no FFI
wrappers, no `*-sys` crates — every codec, container, and filter is
implemented from the spec in safe Rust.

## Get started

The two user-facing binaries:

### `oxideav` — the CLI

Probe, remux, transcode, run JSON transcode graphs. Drop-in on servers
and pipelines; one static binary, no system deps.

```sh
cargo install oxideav-cli           # from crates.io
# or grab a pre-built binary from the latest release:
# https://github.com/OxideAV/oxideav-workspace/releases
```

```sh
oxideav list                            # registered codecs + containers
oxideav probe video.mp4                 # demux probe + metadata
oxideav transcode song.flac song.wav    # remux or transcode
oxideav run job.json                    # run a JSON transcode graph
```

### `oxideplay` — the reference player

SDL2 + TUI frontend. SDL2 is loaded **at runtime via libloading**, so
the binary builds + ships without an SDL2 build-time dep (install SDL2
on the target machine; oxideplay falls back gracefully if it's
missing).

```sh
cargo install oxideplay             # from crates.io
# or grab it pre-built from the release archive:
# https://github.com/OxideAV/oxideav-workspace/releases
```

```sh
oxideplay path/to/file.mkv
oxideplay https://example.com/video.mp4
oxideplay --job job.json            # render a transcode graph live
```

**Pre-built releases** bundle both binaries (`oxideav` + `oxideplay`)
in one per-platform archive — Linux x86_64, macOS universal (Intel +
Apple Silicon), Windows x86_64 — published as a single GitHub Release
per workspace tag.

---

## Using it as a library

OxideAV is a modular framework shipped as ~55 small crates. Each codec
is its own repository and its own crates.io release, so you pick only
what you need and their cadence stays independent of the framework.

Single-crate standalone use is the expected case:

```toml
[dependencies]
oxideav-core = "0.0"       # types
oxideav-codec = "0.0"      # Decoder / Encoder traits + registry
oxideav-g711 = "0.0"       # or any other codec
```

```rust
use oxideav_codec::CodecRegistry;
use oxideav_core::{CodecId, CodecParameters, Frame, Packet, TimeBase};

let mut reg = CodecRegistry::new();
oxideav_g711::register(&mut reg);

let mut params = CodecParameters::audio(CodecId::new("pcm_mulaw"));
params.sample_rate = Some(8_000);
params.channels = Some(1);

let mut dec = reg.make_decoder(&params)?;
dec.send_packet(&Packet::new(0, TimeBase::new(1, 8_000), ulaw_bytes))?;
let Frame::Audio(a) = dec.receive_frame()? else { unreachable!() };
```

Or use the aggregator (`oxideav`) to pull everything in one shot:

```toml
[dependencies]
oxideav = "0.0"
```

---

## Infrastructure crates

| Crate | Role |
|---|---|
| [`oxideav-core`](https://github.com/OxideAV/oxideav-core) | Packet / Frame / TimeBase / PixelFormat / Error |
| [`oxideav-codec`](https://github.com/OxideAV/oxideav-codec) | `Decoder` + `Encoder` traits and registry |
| [`oxideav-container`](https://github.com/OxideAV/oxideav-container) | `Demuxer` + `Muxer` traits and content-based probe registry |
| [`oxideav-pixfmt`](https://github.com/OxideAV/oxideav-pixfmt) | Pixel-format conversion, palette quantisation, dither |
| [`oxideav-pipeline`](https://github.com/OxideAV/oxideav-pipeline) | Source → transforms → sink composition |
| [`oxideav-source`](https://github.com/OxideAV/oxideav-source) | URI resolution, file reader, `BufferedSource` prefetch ring |
| [`oxideav-http`](https://github.com/OxideAV/oxideav-http) | HTTP(S) source driver (ureq + rustls) |
| [`oxideav-job`](https://github.com/OxideAV/oxideav-job) | JSON transcode graph + pipelined multithreaded executor |
| [`oxideav-audio-filter`](https://github.com/OxideAV/oxideav-audio-filter) | Volume / NoiseGate / Echo / Resample / Spectrogram |
| [`oxideav-id3`](https://github.com/OxideAV/oxideav-id3) | ID3v1/v2.2/v2.3/v2.4 tag parser |

## Format coverage

**Containers** — MP4 · Matroska / WebM · Ogg · AVI · FLAC · MP3 · WAV / slin · IFF / 8SVX · PNG / APNG · GIF · JPEG · WebP · AMV · MOD · S3M

**Audio codecs** — PCM · AAC-LC · FLAC · Vorbis · Opus · CELT · Speex · MP1 · MP2 · MP3 · GSM · G.711 · G.722 · G.723.1 · G.728 · G.729

**Video codecs** — MJPEG · FFV1 · MPEG-1 · MPEG-4 Part 2 · H.263 · H.264 (partial) · H.265 (parse) · AV1 (parse) · VP8 · VP9 (partial) · Theora · ProRes 422 · AMV

**Image codecs** — PNG / APNG · GIF · WebP (lossy + lossless) · JPEG (via MJPEG)

**Subtitles** — SRT · WebVTT · ASS / SSA · TTML · SAMI · MicroDVD · MPL2 · MPsub · VPlayer · PJS · AQTitle · JACOsub · RealText · SubViewer 1/2 · EBU STL · PGS · DVB · VobSub

---

## Contributing

Clone [`oxideav-workspace`](https://github.com/OxideAV/oxideav-workspace)
plus any sibling crate you want to hack on, then run
`scripts/dev-patch.sh`. The generated `.cargo/config.toml` wires every
local sibling into the workspace via `[patch.crates-io]`, so
`cargo run -p oxideplay` picks up your local edits without a
re-publish.

## License

Every crate is MIT-licensed. Copyright © 2026 Karpelès Lab Inc.
