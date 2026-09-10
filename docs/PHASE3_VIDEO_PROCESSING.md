# Phase 3: Video Processing - Implementation Guide

## Overview

Phase 3 focuses on video processing capabilities:
- Download videos from YouTube
- Extract frames and audio
- Combine audio with video
- Convert video formats
- Process video metadata

## Components Created

### 1. **Video Processor** (`utils/video_processor.py`)

Handles all video operations using OpenCV and FFmpeg.

**Key Features:**
- Get video metadata (duration, FPS, resolution)
- Extract frames at intervals or specific timestamps
- Extract audio from video
- Convert video formats
- Combine video and audio
- Trim video
- Add text overlays

**Supported Formats:** MP4, AVI, MOV, MKV, WEBM

### 2. **YouTube Downloader** (`utils/youtube_downloader.py`)

Downloads videos from YouTube using `yt-dlp`.

**Key Features:**
- Download videos from YouTube
- Download audio only
- Custom video ID support
- Automatic format detection

### 3. **Video Routes** (`routes/video.py`)

REST API endpoints for video operations.

## API Endpoints

### Video Upload
```
POST /video/upload
Content-Type: multipart/form-data

file: [video file]

Response:
{
  "success": true,
  "file_id": "abc12345",
  "file_path": "./data/uploads/abc12345.mp4",
  "duration": 202.5,
  "fps": 30.0,
  "width": 1920,
  "height": 1080,
  "size": 104857600
}
```

### Download from YouTube
```
POST /video/download-youtube
Content-Type: application/json

{
  "youtube_url": "https://www.youtube.com/watch?v=8QBaLp1C-G8",
  "video_id": "rain-afternoon-walk"
}

Response:
{
  "success": true,
  "file_id": "rain-afternoon-walk",
  "file_path": "./data/uploads/rain-afternoon-walk.mp4",
  "duration": 202.5,
  "fps": 30.0,
  "width": 1920,
  "height": 1080,
  "size": 104857600,
  "message": "Video downloaded successfully. Duration: 202.50s"
}
```

### Get Video Info
```
GET /video/info/{file_id}

Response:
{
  "file_id": "abc12345",
  "duration": 202.5,
  "fps": 30.0,
  "width": 1920,
  "height": 1080,
  "frame_count": 6075,
  "format": "mp4",
  "size": 104857600
}
```

### Extract Frames
```
POST /video/extract-frames/{file_id}?interval=2.0

Response:
{
  "success": true,
  "frame_count": 101,
  "frames": [
    "./data/temp/abc12345_frames/frame_0000_0.00s.jpg",
    "./data/temp/abc12345_frames/frame_0001_2.00s.jpg",
    ...
  ],
  "message": "Extracted 101 frames at 2.0s intervals"
}
```

### Extract Single Frame
```
POST /video/extract-single-frame/{file_id}?timestamp=10.5

Response:
{
  "success": true,
  "file_id": "abc12345",
  "timestamp": 10.5,
  "frame_path": "./data/temp/abc12345_frame_10.50s.jpg",
  "message": "Frame extracted at 10.5s"
}
```

### Extract Audio from Video
```
POST /video/extract-audio/{file_id}

Response:
{
  "success": true,
  "file_id": "abc12345",
  "audio_path": "./data/temp/abc12345_audio.wav",
  "message": "Audio extracted successfully"
}
```

### Combine Audio and Video
```
POST /video/combine-audio-video
Content-Type: application/json

{
  "video_file_id": "rain-afternoon-walk",
  "audio_file_id": "generated-music",
  "output_file_id": "final-video"
}

Response:
{
  "success": true,
  "file_id": "final-video",
  "file_path": "./data/output/final-video.mp4",
  "duration": 202.5,
  "size": 150000000,
  "message": "Video and audio combined successfully"
}
```

### Health Check
```
GET /video/health

Response:
{
  "service": "video",
  "status": "healthy",
  "youtube_downloader_available": true
}
```

## Setup & Dependencies

### Install Required Tools

**FFmpeg** (Required for video processing)
```bash
# macOS
brew install ffmpeg

# Ubuntu/Debian
sudo apt-get install ffmpeg

# Windows
# Download from https://ffmpeg.org/download.html
```

**yt-dlp** (For YouTube downloads)
```bash
pip install yt-dlp
```

### Update Backend Requirements
```bash
cd backend
pip install -r requirements.txt
```

## Complete Workflow: Rain Afternoon Walk

### Step 1: Download Video from YouTube
```bash
curl -X POST "http://localhost:8000/video/download-youtube" \
  -H "Content-Type: application/json" \
  -d '{
    "youtube_url": "https://www.youtube.com/watch?v=8QBaLp1C-G8",
    "video_id": "rain-afternoon-walk"
  }'
```

Save the returned `file_id` (e.g., "rain-afternoon-walk")

### Step 2: Generate Sync Music
```bash
curl -X POST "http://localhost:8000/audio/generate" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Soft ambient garden music with strings and piano, romantic afternoon with balloons",
    "duration": 30,
    "temperature": 0.8
  }'
```

Save the returned `file_id` (e.g., "a1b2c3d4")

### Step 3: Get Video Duration
```bash
curl "http://localhost:8000/video/info/rain-afternoon-walk"
```

Note the duration (e.g., 202.5 seconds)

### Step 4: Extract Sample Frame
```bash
curl -X POST "http://localhost:8000/video/extract-single-frame/rain-afternoon-walk?timestamp=10.0"
```

### Step 5: Extract Audio for Reference
```bash
curl -X POST "http://localhost:8000/video/extract-audio/rain-afternoon-walk"
```

### Step 6: Combine Generated Music with Video
```bash
curl -X POST "http://localhost:8000/video/combine-audio-video" \
  -H "Content-Type: application/json" \
  -d '{
    "video_file_id": "rain-afternoon-walk",
    "audio_file_id": "a1b2c3d4",
    "output_file_id": "final-music-video"
  }'
```

Final video will be at: `./data/output/final-music-video.mp4`

## Important Notes

### FFmpeg Requirements

Must be installed and available in system PATH. Test with:
```bash
ffmpeg -version
```

### YouTube Downloads

- Respects YouTube's robots.txt and rate limiting
- May fail if video is age-restricted or not available
- Check yt-dlp is installed: `yt-dlp --version`

### Video Formats

- Input: MP4, AVI, MOV, MKV, WEBM
- Output: MP4 (H.264 codec, AAC audio)

### File Storage

- Videos uploaded/downloaded: `./data/uploads/`
- Generated videos: `./data/output/`
- Temporary files (frames): `./data/temp/`

## Troubleshooting

### FFmpeg not found
```bash
# Check installation
which ffmpeg

# Reinstall if needed
brew install ffmpeg  # or apt-get install ffmpeg
```

### YouTube download fails
```bash
# Update yt-dlp
pip install --upgrade yt-dlp

# Test download
yt-dlp "https://www.youtube.com/watch?v=8QBaLp1C-G8"
```

### Video processing timeout
- Reduce video resolution
- Use smaller duration segments
- Check system resources (disk space, memory)

## Next Steps (Phase 2)

Now that audio and video APIs are complete, build the React frontend with:
- Dashboard for project management
- Music generation UI
- Video upload/download interface
- Timeline editor for sync
- Real-time preview player
- Export functionality
