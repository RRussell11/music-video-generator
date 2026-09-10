# Phase 1: Audio Generation API - Implementation Guide

## Overview

Phase 1 focuses on building the backend API for music generation using Hugging Face models. This enables users to generate music from text prompts and manage audio files.

## Your Project Integration

**YouTube Video:** https://www.youtube.com/watch?v=8QBaLp1C-G8
- **Title:** Rain Afternoon Walk
- **Scene:** Young woman in floral dress with balloons meeting young man in garden
- **Integration:** Will be used in Phase 2 React UI for timeline sync

## Components Created

### 1. **HuggingFace Model Integration** (`models/huggingface_model.py`)
- Communicates with Hugging Face Inference API
- Uses `facebook/musicgen-small` by default (can be configured)
- Supports custom duration and temperature parameters
- Error handling for API timeouts and failures

**Key Features:**
- Generate music from text prompts
- Control generation parameters (duration, temperature)
- Model information endpoint

### 2. **Audio Processing** (`utils/audio_processor.py`)
- Audio format conversion (WAV, MP3, FLAC, OGG)
- Normalization to target loudness
- Audio mixing capabilities
- Trim, fade in/out effects
- Duration calculation
- Uses librosa and soundfile for processing

**Key Features:**
- Load/save audio in multiple formats
- Normalize to prevent distortion
- Mix multiple audio tracks
- Apply audio effects (fade in/out, trim)

### 3. **File Management** (`utils/file_handler.py`)
- Unique filename generation
- File validation (format, size)
- Directory management
- Support for audio and video formats
- Cleanup utilities

**Configuration:**
- Max audio file: 100MB
- Max video file: 500MB
- Supported audio: WAV, MP3, FLAC, OGG, M4A
- Supported video: MP4, AVI, MOV, MKV, WEBM

### 4. **Audio Routes** (`routes/audio.py`)
REST API endpoints for audio operations:

#### Endpoints

**Generate Music**
```
POST /audio/generate
Content-Type: application/json

{
  "prompt": "Calm ambient music with piano and strings for garden scene",
  "duration": 30,
  "temperature": 1.0,
  "description": "Optional metadata"
}

Response:
{
  "success": true,
  "file_id": "a1b2c3d4",
  "file_path": "./data/output/a1b2c3d4.wav",
  "duration": 30.5,
  "message": "Music generated successfully..."
}
```

**Upload Audio**
```
POST /audio/upload
Content-Type: multipart/form-data

file: [audio file]

Response:
{
  "success": true,
  "file_id": "e5f6g7h8",
  "file_path": "./data/uploads/e5f6g7h8.mp3",
  "duration": 45.2,
  "format": "mp3",
  "size": 1024000
}
```

**Get Audio Info**
```
GET /audio/info/{file_id}

Response:
{
  "file_id": "a1b2c3d4",
  "duration": 30.5,
  "format": "wav",
  "size": 2048000
}
```

**Normalize Audio**
```
POST /audio/normalize/{file_id}?target_db=-20.0

Response:
{
  "success": true,
  "file_id": "a1b2c3d4",
  "message": "Audio normalized to -20.0 dB"
}
```

**Health Check**
```
GET /audio/health

Response:
{
  "service": "audio",
  "status": "healthy",
  "huggingface_available": true
}
```

## Setup Instructions

### 1. Install Dependencies
```bash
cd backend
pip install -r requirements.txt
```

### 2. Configure Environment
```bash
cp .env.example .env
```

Edit `.env` and add:
```
HUGGINGFACE_API_KEY=hf_your_token_here
HUGGINGFACE_MODEL=facebook/musicgen-small
```

Get your API key from: https://huggingface.co/settings/tokens

### 3. Run Backend
```bash
python main.py
```

API will be available at: http://localhost:8000

API Documentation: http://localhost:8000/docs

## Testing the API

### Using cURL

**Generate Music for your video:**
```bash
curl -X POST "http://localhost:8000/audio/generate" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Soft ambient garden music with strings and piano, romantic afternoon atmosphere",
    "duration": 30,
    "temperature": 0.8
  }' > output.json
```

**Check Health:**
```bash
curl http://localhost:8000/audio/health
```

### Using Python

```python
import requests

# Generate music for Rain Afternoon Walk
response = requests.post(
    "http://localhost:8000/audio/generate",
    json={
        "prompt": "Uplifting romantic music with acoustic guitar and strings for garden scene with balloons",
        "duration": 30,
        "temperature": 0.9
    }
)

result = response.json()
print(f"Generated: {result['file_path']}")
print(f"Duration: {result['duration']}s")
```

### Using Swagger UI

Visit http://localhost:8000/docs and interact with endpoints directly

## Important Notes

### Hugging Face Models

- **facebook/musicgen-small**: Fast, lower quality (~15s generation time)
- **facebook/musicgen-medium**: Better quality (~25s generation time)
- **facebook/musicgen-large**: Best quality (~60s generation time)

Model selection impacts generation time. Start with `small` for testing.

### Duration Limits

- Minimum: 5 seconds
- Maximum: 30 seconds
- API will auto-clamp to this range

### Temperature Parameter

- 0.1: Very predictable, less creative
- 0.5: Balanced
- 1.0: Maximum creativity, more variation

## Common Issues

### "HUGGINGFACE_API_KEY not set"
- Ensure `.env` file exists with API key
- Check key format (starts with `hf_`)

### Timeout Errors
- Model may be cold-starting
- Wait a moment and retry
- Consider using a smaller model

### CUDA Out of Memory
- Use smaller model
- Reduce duration
- Restart server

## Next Steps (Phase 2)

- Build React frontend with UI components
- Create timeline editor for sync
- Add project management
- Implement video preview with your YouTube video
- Upload video and sync with generated audio

## Files Modified/Created

```
backend/
├── models/
│   └── huggingface_model.py (NEW)
├── routes/
│   └── audio.py (NEW)
├── utils/
│   ├── audio_processor.py (NEW)
│   └── file_handler.py (NEW)
├── main.py (UPDATED)
└── requirements.txt (UPDATED)
```

## API Response Codes

- **200**: Success
- **400**: Bad request (validation error)
- **404**: File not found
- **500**: Server error
- **503**: Service unavailable (HF API issue)
