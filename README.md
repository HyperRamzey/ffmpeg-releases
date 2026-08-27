# ffmpeg-releases

FFmpeg-only release pipeline for HyperRamzey's self-compiled Windows
FFmpeg builds. Companion mpv builds release separately at
[HyperRamzey/mpv-build](https://github.com/HyperRamzey/mpv-build).

> [!CAUTION]
> **THE BINARIES IN THIS REPO'S RELEASES ARE NOT REDISTRIBUTABLE —
> PERSONAL USE ONLY.** FFmpeg is configured with
> `--enable-gpl --enable-version3 --enable-nonfree` and links GPL
> libraries (x264, x265, xvid, ...) plus the **nonfree** Fraunhofer
> FDK-AAC encoder. See [NOTICE.md](NOTICE.md).

## What gets built

Four CPU/GPU targets, clang 22 (MSYS2 CLANG64), every external library
self-compiled from git masters via the
[deps-build](https://github.com/HyperRamzey/deps-build) framework:

| Bundle          | CPU                 | GPU                  | CUDA arch |
|-----------------|---------------------|----------------------|-----------|
| `ffmpeg-zn3`    | Ryzen 5700X3D (znver3) | RTX 5070 (Blackwell) | sm_120a |
| `ffmpeg-zn2`    | Zen2 (znver2)       | GTX 1650M (Pascal)   | sm_75   |
| `ffmpeg-11700`  | i7-11700 (rocketlake) | RTX 4080 (Ada)     | sm_89   |
| `ffmpeg-3050`   | Zen2 (znver2)       | RTX 3050M (Ampere)   | sm_86   |

Each zip is a portable dir: `ffmpeg.exe`, `ffplay.exe`, `ffprobe.exe`
plus all runtime DLLs (libplacebo-371 with shaderc SPIR-V, etc.).

Highlights: Dolby Vision P7 FEL capable libplacebo filter, NVENC/NVDEC
(clang NVPTX, no CUDA toolkit needed at build time), and **Apple
AudioToolbox AAC (`aac_at`)** via the wat4ff wrapper.

## Apple AAC (`aac_at`) runtime requirement

`aac_at` wraps Apple's proprietary CoreAudio encoder through
[wat4ff](https://github.com/chrdev/wat4ff). The wrapper lazy-loads
Apple's DLLs at runtime — they are **not shipped** in the zips (Apple's
property). To use `aac_at`, either:

- install iTunes / Apple Application Support, or
- place the QTfiles64 DLL set (see
  [AnimMouse/QTFiles](https://github.com/AnimMouse/QTFiles)) in a
  `QTfiles64` folder next to `ffmpeg.exe`:

```text
  |   ffmpeg.exe
  \-- QTfiles64
      |   ASL.dll
      |   CoreAudioToolbox.dll
      |   CoreFoundation.dll
      |   icudt62.dll
      |   libdispatch.dll
      |   libicuin.dll
      |   libicuuc.dll
      |   objc.dll
```

Without the DLLs everything else still works: native `aac`,
`libfdk_aac` (highest-quality standalone AAC encoder) and `aac_mf`.

Usage: `ffmpeg -i input.mkv -c:a aac_at -q 4 output.mkv`
(`-q 0` best quality … `-q 14` smallest; `-profile:a 4` for HE-AAC).

## Pipeline

`.github/workflows/release.yml` mirrors the local build flow 1:1
(same scripts from [deps-build](https://github.com/HyperRamzey/deps-build)
and [ffmpeg-build](https://github.com/HyperRamzey/ffmpeg-build)):

```text
sync-sources -> deps x4 (incl. libplacebo) -> ffmpeg x4 -> release
```

Trigger with **Actions → release → Run workflow** (optionally pass a
`release_tag`), or push a `v*` tag. Every successful run posts a
release with the four zips.

## Source repos

- Build scripts: [HyperRamzey/ffmpeg-build](https://github.com/HyperRamzey/ffmpeg-build)
- Dependency framework: [HyperRamzey/deps-build](https://github.com/HyperRamzey/deps-build)
- mpv pipeline + releases: [HyperRamzey/mpv-build](https://github.com/HyperRamzey/mpv-build)
