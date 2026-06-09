# Rust Backend - Architecture

**Status:** Production Ready with Real YouTube Integration

---

## System Architecture

```
Client (Android Device on 192.168.1.28)
    ↓
Mobile App (React Native)
    ↓
Network: WiFi to 192.168.1.27:3000
    ↓
Rust Backend (Axum Framework)
    ├── /health                           → Status check
    ├── /api/youtube/metadata             → Real metadata via yt-dlp
    ├── /api/youtube/download-url         → Format info & file sizes
    └── /api/download/:id/:quality        → File streaming
    ↓
yt-dlp (System Process)
    ├── Extracts video metadata
    ├── Gets available formats
    └── Streams video files
    ↓
YouTube (via yt-dlp)
```

---

## Endpoints

### 1. GET /health
```
Purpose: Verify backend is running
Response: {
  "status": "Backend is running ✅ with Real YouTube Data",
  "timestamp": "2026-06-08T10:30:00Z"
}
```

### 2. POST /api/youtube/metadata
```
Purpose: Get real YouTube video information
Request: {
  "url": "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
}

Response: {
  "youtube_id": "dQw4w9WgXcQ",
  "title": "Rick Astley - Never Gonna Give You Up",
  "duration": 213,
  "uploader": "Rick Astley Official",
  "url": "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
}
```

### 3. POST /api/youtube/download-url
```
Purpose: Get real file sizes and download URL
Request: {
  "youtubeId": "dQw4w9WgXcQ",
  "quality": "hd"
}

Response: {
  "url": "http://127.0.0.1:3000/api/download/dQw4w9WgXcQ/hd",
  "size": 52428800,
  "mime_type": "video/mp4"
}
```

### 4. GET /api/download/:video_id/:quality
```
Purpose: Stream actual video file
Example: GET /api/download/dQw4w9WgXcQ/hd
Response: Binary MP4/MP3 file with proper headers
```

---

## Quality Mapping

```rust
match quality {
    "hdr"      → bestvideo[height=2160]+bestaudio  (4K)
    "dolby"    → bestvideo[height=1440]+bestaudio  (1440p)
    "fhd"      → bestvideo[height=1080]+bestaudio  (1080p)
    "hd"       → bestvideo[height=720]+bestaudio   (720p)
    "standard" → best[height<=480]                 (480p)
    "low"      → worst                             (360p)
    "audio"    → bestaudio[ext=m4a]/bestaudio      (MP3)
}
```

---

## Data Flow

### Metadata Request
```
1. Frontend: POST /api/youtube/metadata {url}
2. Backend: Extract video ID
3. Backend: Call yt-dlp -j url
4. Backend: Parse JSON response
5. Backend: Return title, duration, uploader
6. Frontend: Display video info
```

### Download Request
```
1. Frontend: POST /api/youtube/download-url {youtubeId, quality}
2. Backend: Call yt-dlp with quality filter
3. Backend: Get format info and file size
4. Backend: Return URL + size
5. Frontend: Fetch from /api/download endpoint
6. Frontend: Stream with progress tracking
7. Device: Save file to storage
```

---

## Technology Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Framework** | Axum 0.7 | Web server |
| **Runtime** | Tokio async | Async processing |
| **Language** | Rust 2021 | Performance |
| **Serialization** | Serde + serde_json | JSON handling |
| **CORS** | Tower-HTTP | Cross-origin requests |
| **System Integration** | yt-dlp subprocess | YouTube data |

---

## Performance Characteristics

- **Metadata fetch:** 2-5 seconds (yt-dlp extracts from YouTube)
- **Download URL:** 2-5 seconds (yt-dlp scans formats)
- **File streaming:** Depends on internet speed (typically 1-10 MB/s)
- **Memory usage:** ~30MB idle + file buffer
- **Binary size:** 8.5 MB (stripped release build)

---

## Dependencies

**Runtime:**
- `tokio` - Async runtime
- `axum` - Web framework
- `serde` - JSON serialization
- `tower-http` - CORS middleware

**System:**
- `yt-dlp` - YouTube extraction tool

**Build:**
- `rustc 1.96.0+` - Rust compiler
- `cargo` - Package manager

---

## Configuration

**Port:** 3000 (hardcoded)  
**Host:** 0.0.0.0 (all interfaces)  
**CORS:** Enabled for all origins  
**Timeout:** Depends on yt-dlp response (typically 5-10 seconds)

---

## Error Handling

| Error | Response | Status |
|-------|----------|--------|
| Invalid URL | "Invalid YouTube URL" | 400 |
| Video not found | "Failed to fetch video" | 404 |
| Format error | "yt-dlp error: ..." | 500 |
| Network error | Connection refused | 500 |

---

## Future Improvements

1. **Caching** - Cache metadata for 1 hour per video
2. **Connection pooling** - Reuse yt-dlp processes
3. **Streaming** - Direct streaming from yt-dlp output
4. **Rate limiting** - Throttle requests per client
5. **Logging** - Structured logging for debugging
6. **Database** - Store metadata for faster lookups
7. **Quality negotiation** - Auto-select quality by bandwidth

---

## Files

- `src/main.rs` - Complete backend implementation
- `Cargo.toml` - Dependencies
- `Cargo.lock` - Dependency lock file
