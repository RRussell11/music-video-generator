# Phase 2: React UI & Timeline Editor - Implementation Guide

## Overview

Phase 2 builds a complete React frontend for the Music Video Generator with:
- Project management dashboard
- Real-time media player
- Timeline editor for audio-video sync
- Music generation UI with AI prompts
- Video upload & YouTube download
- Export dialog for final output

## Project Structure

```
frontend/
├── public/
│   └── index.html
├── src/
│   ├── components/
│   │   ├── CreateProjectForm.js
│   │   ├── ExportDialog.js
│   │   ├── MediaPlayer.js
│   │   ├── MusicGenerator.js
│   │   ├── Timeline.js
│   │   └── VideoUploader.js
│   ├── pages/
│   │   ├── Dashboard.js
│   │   ├── Editor.js
│   │   └── Projects.js
│   ├── services/
│   │   └── ApiClient.js
│   ├── styles/
│   │   ├── CreateProjectForm.css
│   │   ├── Dashboard.css
│   │   ├── Editor.css
│   │   ├── ExportDialog.css
│   │   ├── MediaPlayer.css
│   │   ├── MusicGenerator.css
│   │   ├── Projects.css
│   │   ├── Timeline.css
│   │   └── VideoUploader.css
│   ├── App.js
│   ├── App.css
│   ├── index.js
│   └── index.css
└── package.json
```

## Components Overview

### Pages

**Dashboard.js**
- Welcome page with feature cards
- Project creation form
- Quick start information

**Projects.js**
- List all projects with filtering
- Project cards showing metadata
- Open/delete project actions

**Editor.js**
- Main editing interface
- Tab navigation (Preview, Music, Video, Timeline)
- Integration of all sub-components
- Export button

### Components

**CreateProjectForm.js**
- Create new music video projects
- Pre-populated with example: "Rain Afternoon Walk"
- YouTube URL input
- Description field

**MediaPlayer.js**
- Video playback with controls
- Audio synchronization
- Timeline progress bar
- Play/Pause controls
- Time display

**Timeline.js**
- Visual representation of video and music tracks
- Sync offset adjustment
- Ruler with time markers
- Track duration display
- Real-time sync information

**MusicGenerator.js**
- AI music generation interface
- Text prompt input for music description
- Duration control (5-30 seconds)
- Temperature/creativity slider
- Music upload alternative
- Status feedback

**VideoUploader.js**
- Video file upload
- YouTube video download integration
- Video ID customization
- Drag-and-drop support
- Status messages

**ExportDialog.js**
- Export format selection (MP4, WebM, AVI)
- Quality settings
- Project metadata display
- Sync offset review
- Export progress feedback

### Services

**ApiClient.js**
- HTTP client for backend communication
- Automatic error handling
- Form data support
- Configurable base URL (via REACT_APP_API_URL)

## Setup & Installation

### Prerequisites
- Node.js 16+ and npm
- Backend API running on http://localhost:8000

### Install Dependencies
```bash
cd frontend
npm install
```

### Environment Variables
Create `.env` file:
```
REACT_APP_API_URL=http://localhost:8000
```

### Run Development Server
```bash
npm start
```

App opens at http://localhost:3000

### Build for Production
```bash
npm run build
```

## Features & Workflows

### Creating a Project

1. Click "➕ Create New Project" on Dashboard
2. Fill in project details (pre-populated with Rain Afternoon Walk example)
3. Optionally add YouTube URL
4. Click "Create Project"
5. Redirected to Editor

### Generating Music

1. Go to Editor → Music tab
2. Write music description in AI prompt field
3. Adjust duration (5-30 seconds)
4. Adjust temperature (0.1-1.0 for creativity)
5. Click "✨ Generate Music"
6. Wait for generation to complete
7. Music automatically loaded into project

### Uploading Video

1. Go to Editor → Video tab
2. **Option A:** Upload local video file
   - Drag and drop or click to browse
   - Select MP4, AVI, MOV, MKV, or WEBM file
3. **Option B:** Download from YouTube
   - Paste YouTube URL
   - Optionally set custom video ID
   - Click "📥 Download Video"

### Syncing Audio & Video

1. Go to Editor → Timeline tab
2. View video and music tracks visually
3. Use "Sync Offset" slider to adjust timing
   - Positive = Audio starts later
   - Negative = Audio starts earlier
4. Real-time preview updates
5. Offset saved with project

### Previewing

1. Go to Editor → Preview tab
2. Click "▶️ Play" to start playback
3. Both video and audio play synchronized
4. Adjust timeline if needed

### Exporting

1. Ensure both video and music are loaded
2. Click "📤 Export" button
3. In dialog, select format and quality
4. Review project details and sync offset
5. Click "📤 Export"
6. Video combines audio + video with FFmpeg
7. Final MP4 saved to `./data/output/`

## API Integration

### Audio Endpoints
- `POST /audio/generate` - Generate music
- `POST /audio/upload` - Upload music
- `GET /audio/info/{id}` - Get audio metadata

### Video Endpoints
- `POST /video/upload` - Upload video
- `POST /video/download-youtube` - Download from YouTube
- `GET /video/info/{id}` - Get video metadata
- `POST /video/combine-audio-video` - Combine for export

### Error Handling

All API errors are caught and displayed to user:
- Network errors show connection message
- API errors show detail from backend
- Loading states prevent duplicate requests
- Success messages confirm operations

## Styling

### Design System
- **Primary Color:** #667eea (Blue)
- **Secondary Color:** #764ba2 (Purple)
- **Accent Color:** #f5576c (Red for video)
- **Background:** Linear gradient purple

### Responsive Design
- Mobile-first approach
- Breakpoint at 768px
- Touch-friendly controls
- Flexible layouts

### Components Use
- Global `App.css` for base styles
- Component-specific CSS files
- Consistent spacing and typography
- Smooth transitions and animations

## Local Storage

Projects are persisted in browser localStorage:
- Auto-saved after each update
- Survives page refresh
- Can be cleared via browser settings

To reset projects:
```javascript
localStorage.removeItem('projects');
```

## Troubleshooting

### API Connection Error
- Ensure backend is running on port 8000
- Check REACT_APP_API_URL in .env
- Verify CORS is enabled in FastAPI

### Music Generation Timeout
- Hugging Face models may be cold-starting
- Retry after a moment
- Check Hugging Face API key is valid

### Video Download Fails
- Ensure yt-dlp is installed on backend
- Check YouTube URL is valid and public
- Video may be age-restricted

### Export Takes Long
- FFmpeg processing is CPU intensive
- Duration depends on file size and system
- Don't refresh browser during export

## Next Steps

- Add waveform visualization for audio
- Implement lyric sync with timeline
- Add effects and filters panel
- Support for multiple tracks
- Real-time video preview
- Cloud storage integration
- Collaborative editing

## Browser Support

- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

## Performance Tips

- Use smaller video files for faster processing
- Lower quality settings for quicker export
- Close other apps during export
- Clear browser cache if slowdown occurs
