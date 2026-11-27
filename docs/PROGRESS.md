# Progress

## Current focus
Rebuilding after the 2026-11-26 Windows reinstall (new motherboard + R9700). Then: run the 30B model on the R9700 and build the agent console.

## Done
- **2026-11-26**: New motherboard (ASUS ProArt X870E-CREATOR WIFI) and **Radeon AI PRO R9700 installed**. Windows reinstalled, so all tools and chat history on C: were lost.
- **2026-11-26**: **Moved the project off D: to `C:\Users\dylan\Downloads\Korvellan-Studios`** (the working copy; `C:\Korvellan-Studios` was a temporary copy and its edits were merged here). New rule: never use D: or F: (see CLAUDE.md).
- **2026-11-26**: Reinstalled uv, ffmpeg, Git, Node LTS, deno, Ollama 0.40.18 (winget) and yt-dlp (`uv tool`). Rebuilt both Python environments. Ollama sees the R9700 via ROCm (31.9 GB) and the 5070. Models are in the default C: location.
- **2026-11-20**: Assessed the dev environment (see [ARCHITECTURE.md](ARCHITECTURE.md)).
- **2026-11-20**: Rebranded the repo from Answer North to Korvellan Studios; added project docs.
- **2026-11-20**: Renamed the local folder to `Korvellan-Studios` and the GitHub repo to `Korvellan-Studios`.
- **2026-11-21**: Chose the first video products: **short vertical clips** and **faceless videos**.
- **2026-11-21**: Installed ffmpeg 9.1 (winget). Verified NVIDIA hardware encoding (h264/hevc/av1_nvenc) on the RTX 5070.
- **2026-11-21**: Agreed on the video pipeline design: one `video-pipeline/` project, shared stages, Clipper recipe first (see [ARCHITECTURE.md](ARCHITECTURE.md)).
- **2026-11-21**: Built the **transcribe** stage: faster-whisper on the RTX 5070 (~30× real time), word-level timestamps → `transcript.json`.
- **2026-11-21**: Installed yt-dlp; added `video-pipeline/input/` (gitignored) for source videos.
- **2026-11-21**: Transcribed a real 48-min gaming video: 92% avg word confidence, ~3 min.
- **2026-11-21**: Built the **pick** stage: local LLM (qwen3.5:9b) picks and ranks the top 10 clips (20–60 s) → `clips.json`. ~2.5 min for a 48-min video.
- **2026-11-21**: Built the **render** stage: picks → 1080×1920 MP4s on NVENC (~10 s/clip, ~10 Mbps). Layouts: `blur` (user's choice) and `crop`.
- **2026-11-21**: Built **captions**: word-by-word, spoken word highlighted yellow, burned in during render. Font size 100, max 16 chars per caption.
- **2026-11-21**: Built **`clipper`** (one command: link or file → download → transcribe → pick → render, reusing finished steps) and **`download`** (yt-dlp into `input/`). Tested end-to-end from a YouTube link: all 10 captioned clips in 1m 39s.

- **2026-11-21**: User ran the full `clipper` on a 40-min Matan Even video: 9 captioned clips (private test only; not rights-cleared for posting).
- **2026-11-21**: Transcription is now **fully offline** (loads the Whisper model locally, never contacts Hugging Face unless the model is missing).
- **2026-11-21**: Added **`auto` layout** (new default): YuNet face detection on 15 frames per video picks `split` (face cam on top, gameplay below), `crop` (centered on one person), or `blur` (groups / no face). Verified: gaming video → split, Matan group video → blur. Installed `opencv-python-headless`.

- **2026-11-21**: Rebuilt **prospect-finder** with two commands:
  - `creators` (new): finds YouTube creators posting long videos with few/no Shorts → `data/creator_prospects.csv`. Tested: "business podcast" → 12 creators, 4 HIGH, in 24 s.
  - `search` (fixed): industry now actually filters (OSM tag presets + name match), any capitalization works (geocoded), searches a radius around the city, flags businesses with no website. **Not yet verified:** the free OpenStreetMap servers were overloaded during testing.

- **2026-11-21**: Local search now covers the **Twin Cities metro** (7 counties, default) or specific cities, and runs on a **local copy of Minnesota's OpenStreetMap data** (`update-map`; 41k businesses indexed, searches in <1 s, offline). The public OSM servers were at capacity all day, so this replaced them. Installed `osmium`. Coverage: thin for trades (29 plumbers metro-wide), good for storefronts (303 dentists, 867 salons).

## Feedback so far
- Test video: timestamps were decent, but the source itself is low-energy. Need a livelier Creative Commons podcast/interview.
- No caption censoring for now (user's call).

## Next
1. Test on a livelier Creative Commons podcast/interview.
2. ~~Install deno~~ (installed 2026-11-26); confirm yt-dlp no longer warns about a missing JavaScript runtime.
3. Later: speaker-following crop for group shows (crop onto whoever is talking).
4. Later: loudness detection to catch non-verbal moments (screams, big plays) in gaming content.
5. Later: optional caption censoring.

## Waiting on
- **Radeon AI PRO R9700**: installed 2026-11-26. Remaining:
  1. ~~AMD driver, verify detection~~ (done: Ollama sees it via ROCm).
  2. Ollama on the R9700 only (hide the NVIDIA card from it); pull a ~30B model; switch `llm.model` in `video-pipeline/config.yaml`; compare picks against the 9B model.
  3. **Build the agent console** (`console/`, approved for after the install): chat in plain English, local LLM calls the existing tools (creators, search, download, clipper, open folder, outreach drafts). Include **lead tools**: query `local_prospects.csv` / `creator_prospects.csv` ("salons in Edina with a phone"), draft pitches, and track outreach (contacted / replied / follow-up dates) in a small local database (not the CSV, since Excel locks open files), with export to Excel and a one-time import of any notes the user added in Excel. Phases: terminal chat → background jobs + progress → local web UI with clip previews. Tools allowlisted, no deletes, D: and F: blocked in code, confirm before long jobs.

## Known issues
- **prospect-finder `search`**: OpenStreetMap is incomplete for trades (e.g. only 29 plumbers in the metro) and "no website" often means "not listed". If trade leads matter, consider Google Places API (paid). The `twin-cities` region is a rectangle, so it includes a few towns just outside the 7 counties (e.g. Big Lake, Buffalo, Monticello).
