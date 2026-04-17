# OxideAV

**Pure-Rust media transcoding and streaming.** No C libraries, no FFI
wrappers, no `*-sys` crates — every codec, container, and filter is
implemented from the spec in safe Rust.

The **workspace** — [oxideav-workspace](https://github.com/OxideAV/oxideav-workspace) — is the
dev hub. It ships the CLI (`oxideav`), the reference player
(`oxideplay`), the aggregator crate (`oxideav`), and the integration
test suite. Clone it to build everything end-to-end.

Each codec and container lives in its **own repository and its own
crates.io release**, so their cadence is independent: a stable codec
like G.711 never has to re-publish just because a video decoder
shipped a bug fix. Pick only what you need.

---

## Infrastructure

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

## Containers

MP4 · Matroska / WebM · Ogg · AVI · FLAC · MP3 · WAV / slin · IFF / 8SVX · PNG / APNG · GIF · JPEG · WebP · AMV · MOD · S3M

## Audio codecs

PCM · AAC-LC · FLAC · Vorbis · Opus · CELT · Speex · MP1 · MP2 · MP3 · GSM · G.711 · G.722 · G.723.1 · G.728 · G.729

## Video codecs

MJPEG · FFV1 · MPEG-1 · MPEG-4 Part 2 · H.263 · H.264 (partial) · H.265 (parse) · AV1 (parse) · VP8 · VP9 (partial) · Theora · ProRes 422 · AMV

## Image codecs

PNG / APNG · GIF · WebP (lossy + lossless) · JPEG (via MJPEG)

## Subtitles

SRT · WebVTT · ASS / SSA · TTML · SAMI · MicroDVD · MPL2 · MPsub · VPlayer · PJS · AQTitle · JACOsub · RealText · SubViewer 1/2 · EBU STL · PGS · DVB · VobSub

---

## Contributing

Clone [`oxideav-workspace`](https://github.com/OxideAV/oxideav-workspace)
plus any sibling crate you want to hack on, then run
`scripts/dev-patch.sh`. The generated `.cargo/config.toml` wires every
local sibling into the workspace via `[patch.crates-io]`, so
`cargo run -p oxideplay` picks up your local edits without a re-publish.

## License

Every crate is MIT-licensed. Copyright © 2026 Karpelès Lab Inc.
