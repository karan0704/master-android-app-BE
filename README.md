# Master Android App - Rust Backend

⚡ **Ultra-lightweight, blazingly fast backend** written in Rust.

Perfect for embedding directly in the mobile app!

## 📋 Prerequisites

Install Rust (if not already installed):

```bash
# macOS/Linux
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Windows
# Download from: https://www.rust-lang.org/tools/install
```

Verify installation:
```bash
rustc --version
cargo --version
```

## 🚀 Getting Started

### Step 1: Build the Project
```bash
cd "D:\a) Apps\Master Android App\Master-Android-App-RustBE - Backend"
cargo build --release
```

This will:
- Download dependencies
- Compile Rust code
- Create optimized binary

**Output:** `target/release/youtube_backend.exe` (Windows) or `target/release/youtube_backend` (Mac/Linux)

### Step 2: Run the Server
```bash
cargo run --release
```

Or run the compiled binary directly:
```bash
./target/release/youtube_backend
```

Expected output:
```
✅ Rust Backend running on http://127.0.0.1:3000

📝 Environment: development
🌐 CORS: Enabled for all origins
```

### Step 3: Test the API

**Health Check:**
```bash
curl http://localhost:3000/health
```

**Fetch Metadata:**
```bash
curl -X POST http://localhost:3000/api/youtube/metadata \
  -H "Content-Type: application/json" \
  -d '{"url":"https://youtube.com/watch?v=dQw4w9WgXcQ"}'
```

**Get Download URL:**
```bash
curl -X POST http://localhost:3000/api/youtube/download-url \
  -H "Content-Type: application/json" \
  -d '{"youtube_id":"dQw4w9WgXcQ","quality":"fhd"}'
```

## 📦 Project Structure

```
Master-Android-App-RustBE/
├── src/
│   └── main.rs              # Main server code
├── Cargo.toml               # Project config & dependencies
├── Cargo.lock               # Dependency lock file (auto-generated)
├── target/
│   └── release/             # Compiled binaries (after build)
└── README.md
```

## 🔌 API Endpoints

### 1. Health Check
```
GET /health
```
**Response:**
```json
{
  "status": "Backend is running ✅",
  "timestamp": "2024-06-02T12:00:00+00:00"
}
```

### 2. Fetch Video Metadata
```
POST /api/youtube/metadata
Content-Type: application/json

{
  "url": "https://youtube.com/watch?v=VIDEO_ID"
}
```

**Response:**
```json
{
  "youtube_id": "VIDEO_ID",
  "title": "Video: VIDEO_ID",
  "duration": 480,
  "uploader": "Sample Creator",
  "url": "https://youtube.com/watch?v=VIDEO_ID"
}
```

### 3. Get Download URL
```
POST /api/youtube/download-url
Content-Type: application/json

{
  "youtube_id": "VIDEO_ID",
  "quality": "fhd"
}
```

**Response:**
```json
{
  "url": "https://mock-download.example.com/VIDEO_ID/fhd",
  "size": 262144000,
  "mime_type": "video/mp4"
}
```

**Supported Qualities:**
- `hdr` - 500 MB
- `dolby` - 350 MB
- `fhd` - 250 MB
- `hd` - 150 MB
- `standard` - 75 MB
- `low` - 40 MB
- `audio` - 20 MB (MP3)

## ⚡ Why Rust?

- **Lightweight:** ~5-10 MB binary size
- **Fast:** Blazingly fast performance
- **Memory Safe:** No memory leaks or crashes
- **Single Binary:** Easy to embed in app
- **Low Battery Drain:** Efficient resource usage

## 📊 Binary Size

After compilation with release optimizations:

```
Release Binary: ~8-10 MB (stripped)
Uncompressed: ~12 MB
Runtime Memory: ~20-30 MB
```

Compare to:
- Node.js: 50-100 MB
- Python: 30-80 MB
- Go: 5-10 MB (similar)

## 🔄 Next Steps

1. ✅ Rust backend created
2. 🔲 Test locally (run the server)
3. 🔲 Integrate with React Native
4. 🔲 Compile to Android/iOS native library
5. 🔲 Embed in mobile app
6. 🔲 Add real YouTube functionality

## 📝 Development

### Build Modes

**Debug (development):**
```bash
cargo build
```
- Slower compilation
- Faster builds
- Better debugging

**Release (production):**
```bash
cargo build --release
```
- Slower compilation
- Smaller binary (~8 MB)
- Maximum performance

### Common Commands

```bash
# Build
cargo build --release

# Run
cargo run --release

# Check code
cargo check

# Format code
cargo fmt

# Lint code
cargo clippy

# Clean build
cargo clean
```

## 📚 Learn Rust

- Official Book: https://doc.rust-lang.org/book/
- Axum Web Framework: https://github.com/tokio-rs/axum
- Tokio Async Runtime: https://tokio.rs/

## 🐛 Troubleshooting

**"cargo not found"**
- Install Rust from: https://www.rust-lang.org/tools/install

**"Failed to resolve"**
- Run: `cargo build` to download dependencies
- Check internet connection

**"Port 3000 already in use"**
- Change port in `src/main.rs` line ~25
- Or kill existing process on port 3000

## 📧 Notes

- Currently uses mock data for YouTube videos
- Next: Integrate with `yt-dlp` for real downloads
- Built with Axum (lightweight) instead of Actix-web (heavier)
