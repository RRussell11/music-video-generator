# Architecture Overview

## System Architecture

```
┌─────────────────────────────────────────────────────────┐
│                   Frontend (React)                      │
│  - Timeline Editor                                      │
│  - Video/Audio Player                                  │
│  - Waveform Visualization                              │
│  - Project Management                                   │
└────────────────────────┬────────────────────────────────┘
                         │ HTTP/REST API
                         ▼
┌─────────────────────────────────────────────────────────┐
│              Backend (FastAPI)                          │
├─────────────────────────────────────────────────────────┤
│ Routes:                                                 │
│  - /audio - Audio processing & generation              │
│  - /video - Video processing & sync                    │
│  - /project - Project management                       │
│  - /export - Export final video                        │
├─────────────────────────────────────────────────────────┤
│ Services:                                               │
│  - Audio Generation (Hugging Face models)              │
│  - Audio Processing (Librosa, Pydub)                   │
│  - Video Processing (OpenCV, FFmpeg)                   │
│  - Sync Management                                      │
├─────────────────────────────────────────────────────────┤
│ Storage:                                                │
│  - Uploaded videos/audio files                         ��
│  - Generated audio outputs                             │
│  - Project metadata                                     │
│  - Temporary files                                      │
└─────────────────────────────────────────────────────────┘
```

## Data Flow

### Music Generation Flow
```
User Input (Lyrics/Prompt)
         ▼
Hugging Face Model (MusicGen)
         ▼
Generated Audio (MP3/WAV)
         ▼
Audio Processing (Effects, Normalization)
         ▼
Store in Project
```

### Video Sync Flow
```
Upload Video
    ▼
Parse Video Metadata (Duration, FPS)
    ▼
Generate/Upload Audio
    ▼
Create Timeline with Sync Points
    ▼
Preview (Real-time sync)
    ▼
Export as Video File
```

## Key Components

### Backend Modules

**models/**
- `huggingface_model.py` - HF model integration
- `audio_processor.py` - Audio processing utilities
- `video_processor.py` - Video processing utilities

**routes/**
- `audio.py` - Audio endpoints
- `video.py` - Video endpoints
- `project.py` - Project management endpoints

**utils/**
- `file_handler.py` - File upload/download
- `sync_manager.py` - Audio-video sync
- `validators.py` - Input validation

### Frontend Components

**components/**
- `Timeline.js` - Timeline editor
- `Player.js` - Media player
- `Waveform.js` - Audio visualization
- `ProjectManager.js` - Project UI
- `ExportDialog.js` - Export settings

**pages/**
- `Dashboard.js` - Main dashboard
- `Editor.js` - Editing interface
- `Projects.js` - Project list

## Database Schema (Future)

```
Projects
├── id (UUID)
├── name (string)
├── description (text)
├── created_at
├── updated_at

Tracks
├── id (UUID)
├── project_id (FK)
├── type (audio/video)
├── file_path
├── duration
├── sync_offset

SyncPoints
├── id (UUID)
├── track_id (FK)
├── timestamp
├── label
```

## API Endpoints (Planned)

### Audio
- `POST /audio/generate` - Generate music from prompt
- `POST /audio/upload` - Upload audio file
- `GET /audio/{id}` - Get audio details
- `DELETE /audio/{id}` - Delete audio

### Video
- `POST /video/upload` - Upload video file
- `GET /video/{id}` - Get video details
- `DELETE /video/{id}` - Delete video

### Project
- `POST /project` - Create project
- `GET /project/{id}` - Get project
- `PUT /project/{id}` - Update project
- `DELETE /project/{id}` - Delete project

### Export
- `POST /export/{project_id}` - Export as video
- `GET /export/{task_id}` - Get export status
