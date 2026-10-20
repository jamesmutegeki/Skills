---
name: video-editing
description: Use for video editing workflows, post-production, color grading, motion graphics, audio processing, transcoding, and automation. Covers FFmpeg, DaVinci Resolve scripting, Premiere automation, and pipeline tooling.
---

# Video Editing

## Principles

- **Non-destructive workflow**: never modify source files. Use proxies, timelines, and renders.
- **Automate the boring parts**: transcoding, backups, proxy generation, exports — script these.
- **Optimize for delivery**: render settings depend on platform (YouTube, Instagram, broadcast) — know your target.

## Pipeline

### 1. Ingestion & Organization
```
Source media → Rename (consistent convention) → Verify integrity (ffmpeg/mediainfo)
→ Generate proxies (1080p ProRes or H.264) → Bin by date/project/scene
```

### 2. Editing Workflow
- Rough cut: assemble selects, establish pacing
- Fine cut: trim, J/L cuts, transitions
- Sound design: dialogue clean (iZotope RX / ffmpeg filters), Foley, music bed, mix (-24 LUFS)
- Color grade: correct exposure/white balance → creative grade → match shots
- Motion graphics: titles, lower thirds, callouts (use templates where possible)

### 3. Export & Delivery

**Common presets:**
| Platform | Codec | Resolution | Bitrate | Audio |
|----------|-------|-----------|---------|-------|
| YouTube | H.264 | 4K/1080p | 16-45 Mbps | AAC 320kbps |
| Instagram Reels | H.264 | 1080×1920 | 12 Mbps | AAC 256kbps |
| Broadcast | DNxHR HQ / ProRes 422 | 1080p | ~145 Mbps | PCM 48kHz |
| Archive | ProRes 422 / DNxHR | Source | Lossless-ish | PCM 48kHz |

## Automation (FFmpeg)

**Proxy generation:**
```
ffmpeg -i input.mp4 -vf "scale=1280:720" -c:v libx264 -preset fast -crf 23 -an proxy.mp4
```

**Batch transcode:**
```
Get-ChildItem *.mp4 | ForEach-Object {
  ffmpeg -i $_ -c:v libx264 -preset medium -crf 18 -c:a aac -b:a 320k "encoded_$($_.Name)"
}
```

**Extract audio:**
```
ffmpeg -i input.mp4 -vn -c:a libmp3lame -q:a 2 audio.mp3
```

## Tools Reference

| Tool | Use Case |
|------|----------|
| DaVinci Resolve | Full color grade + edit (free tier capable) |
| FFmpeg | Batch transcoding, extraction, analysis |
| Audacity / iZotope RX | Audio cleanup and restoration |
| HandBrakeCLI | Compression and format conversion |
| Shutter Encoder | GUI for FFmpeg + batch processing |

## Quality Checklist
- Audio peaks at -6dB, integrated -24 LUFS (broadcast) or -14 LUFS (streaming)
- No clipping or artifacts on export
- Color space: Rec. 709 for web, Rec. 2020 for HDR
- Subtitles rendered or embedded as sidecar (.srt/.vtt)
- All external assets licensed and credited
