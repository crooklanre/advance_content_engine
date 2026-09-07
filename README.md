# Advanced Content Engine

A dynamic, prompt-driven project for campaign content generation.

## What it does
- Reads strategy from prompt/how/plan/gather markdown or direct prompt text.
- Builds a source-aware dynamic plan.
- Executes through an orchestrated task graph.
- Applies reflection-based iterative tuning with weighted quality metrics.
- Renders scripts/captions/images/videos in parallel.
- Supports campaign-aware planning for community/pillar content (not only Apify tutorials).
- Generates richer per-topic packs: `storyboard.txt`, `image_prompt.txt`, `video_prompt.txt`, and `topic.json`.
- Exports `content_calendar.json` and `image_references.json` for campaign operations.
- Optional automatic narration pipeline: TTS (`narration.wav`) + ffmpeg stitched `video_narrated.mp4`.
- Scene-first cinematic pipeline: `scenes.json` -> composition -> delivery exports.
- Delivery exports per topic: `video_1080p.mp4`, `video_4k.mp4`, `shorts_vertical_1080x1920.mp4`, `captions.srt`, `thumbnail.jpg`.
- Logs execution history in SQLite memory and JSON logs.
- Runs full automated test suite with `--autotest`.

## Core architecture
- `orchestrator.py`: task graph with dependency and retry semantics.
- `quality.py`: weighted scoring (seo, coherence, completeness, creativity, source coverage).
- `tuner.py`: reflection loop and repair until target score.
- `memory.py`: persistent run memory (SQLite).
- `pipeline.py`: parallel render + manifest + memory log + optional test gate.

## Quick start
```bash
cd advanced_content_engine
$env:PYTHONPATH = ".\\src"
python -m advanced_content_engine.cli run \
  --prompt "Build community campaign with narrated videos" \
  --plan ..\communitycontentplan.md \
  --output .\output\run_narrated --narrated --tts-rate 1 --cinematic --fetch-images --topic-limit 8
```

## EruditeWBT helper scripts

From repo root:

```powershell
powershell -ExecutionPolicy Bypass -File .\advanced_content_engine\scripts\run_erudite_topic.ps1 -Topic intro_to_tech_community

# batch run all prepared topics
powershell -ExecutionPolicy Bypass -File .\advanced_content_engine\scripts\run_erudite_batch.ps1 -Narrated -Cinematic -FetchImages
```

## Reusable templates

If you want to feed the engine new information later, start from:

- `advanced_content_engine/templates/single_topic_brief.md`
- `advanced_content_engine/templates/multi_topic_campaign.md`
- `advanced_content_engine/templates/product_showcase_brief.md`
- `advanced_content_engine/templates/course_module_brief.md`
- `advanced_content_engine/templates/community_event_brief.md`
- `advanced_content_engine/templates/field_translation_brief.md`

The planner now supports custom `## Topic Brief:` blocks directly.

## Tests
```bash
python -m unittest discover -s tests -p "test_*.py"
```

## Regression benchmarks
- `benchmarks/prompts.json`
- enforced by `tests/test_regression.py`

## Notes
- MP4 rendering is optional and requires `ffmpeg` in PATH.
- Without ffmpeg, GIF videos are produced.
- Narrated MP4 needs both `ffmpeg` and Windows `System.Speech` availability.
- If TTS runs but ffmpeg is missing, you still get `narration.wav` per topic for later stitching.
- You can override ffmpeg location with `ACE_FFMPEG_BIN` if it is not on PATH.
- `--fetch-images` downloads source visuals into `asset_library/raw_images` and topic-level `visual_assets/`.
- Use `--topic-limit` for faster cinematic renders while iterating.



$env:ACE_PERFORMANCE_MODE='preview' $env:ACE_MERMAID_MODE='safe'
$env:ACE_STUDIO_PRESET='ultrafast'
$env:ACE_STUDIO_CRF='30'
$env:ACE_AUDIO_MUX_TIMEOUT='120'
powershell -ExecutionPolicy Bypass -File .\advanced_content_engine\scripts\run_erudite_topic.ps1 -Topic the_advent_of_ai -Cinematic -Narrated



$env:ACE_PERFORMANCE_MODE='preview' $env:ACE_MERMAID_MODE='safe'
$env:ACE_STUDIO_PRESET='ultrafast'
$env:ACE_STUDIO_CRF='30'
$env:ACE_AUDIO_MUX_TIMEOUT='120'
powershell -ExecutionPolicy Bypass -File .\advanced_content_engine\scripts\run_erudite_batch.ps1 -Narrated -Cinematic -FetchImages