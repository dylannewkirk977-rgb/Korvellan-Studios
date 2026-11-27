# Architecture

## Hardware

| Component | Spec | Role |
|---|---|---|
| CPU | Ryzen 9 7950X3D (16 cores / 32 threads) | Video encoding, general compute |
| RAM | 64 GB DDR5 | |
| GPU 1 | RTX 5070, 12 GB (CUDA) | Image/video generation, speech-to-text, text-to-speech |
| GPU 2 | Radeon AI PRO R9700, 32 GB | Large language models (installed 2026-11-26; Ollama sees it via ROCm) |
| C: | NVMe, ~1.5 TB free | OS, tools, active projects |
| C: (models) | Ollama models in the default location (`%USERPROFILE%\.ollama\models`) | Model weights |
| D:, F: | Other drives | **Off limits: tools never read or write here** |

**Why split the GPUs this way:** language models need a lot of memory, and the R9700's 32 GB fits much bigger models. Image, video, and audio tools are mostly built for NVIDIA, so they go on the 5070. Each card does its own job without competing for memory.

**Motherboard:** ASUS ProArt X870E-CREATOR WIFI (installed 2026-11-26, with a Windows reinstall).

## Software

| Tool | Status |
|---|---|
| Ollama 0.40.17 | Installed: `qwen3.5:9b`, `qwen2.5-coder:14b` |
| uv (Python 3.13, 3.15) | Installed |
| Node 26, git, VS Code | Installed |
| opencode | Configured for local Ollama (`opencode.json`) |
| ffmpeg 9.1 | Installed (winget). GPU encoding via NVENC on the 5070 |
| yt-dlp | Installed (`uv tool install yt-dlp`); update with `uv tool upgrade yt-dlp` |
| Docker / WSL | Not installed (not needed yet) |

## Video pipeline (`video-pipeline/`)

One Python project with reusable **stages**, chained into **recipes**:

- **Clipper** (building first): long video → transcribe → AI picks best moments → cut → crop to 9:16 → captions → MP4s
- **Faceless** (later): topic → AI script → voiceover → visuals → captions → MP4

| Stage | Tool | Runs on | Status |
|---|---|---|---|
| Transcribe | faster-whisper (`large-v3-turbo`) | RTX 5070 | ✅ Done |
| Pick moments | Ollama `qwen3.5:9b` | 5070 now, R9700 later | ✅ Done |
| Cut / crop / render | ffmpeg (NVENC) | RTX 5070 | ✅ Done |
| Captions | .ass from word timestamps, burned in | ffmpeg (libass) | ✅ Done |
| Auto layout (split/crop/blur) | OpenCV YuNet face detection | CPU | ✅ Done |

## Design rules
- **One folder per service**, each with its own dependencies.
- **Swappable stages**: each pipeline step (script, voice, visuals, captions, assembly) is a separate module with a clear input and output.
- **Config over code**: model names and endpoints live in config, so moving an LLM to the R9700 is a settings change.
