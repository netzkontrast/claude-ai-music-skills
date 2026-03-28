# MCP Server and Python Tools Infrastructure

**Date:** 2026-03-28
**Scope:** Bitwize Music MCP Server + Python Tools Ecosystem

---

## Overview

The Claude AI Music Skills plugin is powered by a **Model Context Protocol (MCP) server** that exposes 70+ tools for album and track management, combined with a modular **Python tools ecosystem** for audio processing, promo generation, and state management.

### Key Statistics

- **MCP Server:** `bitwize-music-mcp` (stdio transport)
- **Handler Modules:** 14 (organized by domain)
- **Total MCP Tools Exposed:** 70+
- **Tool Categories:** Core queries, lyrics analysis, audio processing, promotion, database, streaming, health checks
- **Python Tool Modules:** 11 subdirectories
- **Test Suite:** ~40 test files covering unit and integration scenarios

---

## MCP Server Architecture

### Server Entry Point: `servers/bitwize-music-server/server.py`

**Transport:** stdio (Claude Code plugin standard)
**Configuration:** `.mcp.json` (registers server with Claude Code harness)

```json
{
  "mcpServers": {
    "bitwize-music-mcp": {
      "type": "stdio",
      "command": "${HOME}/.bitwize-music/venv/bin/python3",
      "args": ["${CLAUDE_PLUGIN_ROOT}/servers/bitwize-music-server/run.py"]
    }
  }
}
```

### State Cache (In-Memory with Disk Persistence)

**Class:** `StateCache` (thread-safe with read-write lock)

**Features:**
- Lazy loading from `~/.bitwize-music/cache/state.json`
- Staleness detection via file mtime comparison
- Automatic rebuild on schema version mismatch
- Session preservation during rebuilds
- Thread-safe concurrent access

**Key Methods:**
- `get_state()` — Load state with staleness check
- `rebuild()` — Force full rebuild from markdown files
- `update_session(**kwargs)` — Update session context atomically
- `_is_stale()` — Check config/state mtime
- `_load_from_disk()` — Load + auto-migrate on version change

### Handler Module Organization

14 handler modules in `servers/bitwize-music-server/handlers/`, each registering tools via `mcp.tool()` decorator:

| Handler | Purpose | Tool Count |
|---------|---------|-----------|
| **core.py** | Album/track queries, sessions, config, search, paths | 17 |
| **content.py** | Markdown overrides, reference files, clipboard formatting | 3 |
| **text_analysis.py** | Homographs, artist names, explicit content, syllables | 7 |
| **lyrics_analysis.py** | Syllable counting, readability, rhyme schemes, plagiarism | 5 |
| **album_ops.py** | Album structure validation, creation | 3 |
| **gates.py** | Pre-generation verification, source checking | 2 |
| **streaming.py** | Streaming URL management, verification | 3 |
| **skills.py** | Skills listing and metadata queries | 2 |
| **status.py** | Album/track status transitions, creation | 2 |
| **promo.py** | Promo directory status, content retrieval | 2 |
| **health.py** | Plugin version, venv health checks | 2 |
| **ideas.py** | Idea management (create, update) | 2 |
| **rename.py** | Album and track renaming | 2 |
| **processing.py** | Audio mastering, mixing, sheet music, promo videos | 15 |
| **database.py** | Tweet/promo management via PostgreSQL | 8 |
| **maintenance.py** | Reset mastering, legacy cleanup, audio migration | 3 |

---

## MCP Tools Catalog

### CORE TOOLS (17) — Album/Track Queries & Sessions

| Tool | Purpose | Parameters |
|------|---------|-----------|
| `find_album` | Find album by name with fuzzy matching | `name` (string) |
| `list_albums` | List all albums with optional status filter | `status_filter` (optional, "In Progress"\|"Complete"\|etc.) |
| `get_track` | Fetch track details and lyrics | `album_slug`, `track_slug` |
| `list_tracks` | List album's tracks with optional status filter | `album_slug`, `status_filter` (optional) |
| `get_session` | Get session context (last album/track/phase) | None |
| `update_session` | Update session context atomically | `album`, `track`, `phase`, `action`, `clear` |
| `rebuild_state` | Force full state rebuild from markdown | None |
| `get_config` | Get resolved configuration | None |
| `get_python_command` | Get venv python command for executing tools | None |
| `get_ideas` | Get album ideas with optional status filter | `status_filter` (optional) |
| `search` | Cross-scope search (albums/tracks/skills/ideas) | `query`, `scope` ("all"\|"albums"\|"tracks"\|"skills"\|"ideas") |
| `get_pending_verifications` | List tracks with unverified sources | None |
| `resolve_path` | Resolve content/audio/documents path | `path_type`, `album_slug`, `genre` |
| `resolve_track_file` | Get absolute path to track markdown | `album_slug`, `track_slug` |
| `list_track_files` | List all track files in album | `album_slug`, `status_filter` (optional) |
| `extract_section` | Extract markdown section from track | `album_slug`, `track_slug`, `section` |
| `update_track_field` | Update track frontmatter field | `album_slug`, `track_slug`, `field`, `value` |
| `get_album_progress` | Get album completion statistics | `album_slug` |

### CONTENT TOOLS (3) — Markdown & Overrides

| Tool | Purpose | Parameters |
|------|---------|-----------|
| `load_override` | Load override file from `{overrides}/` directory | `filename` ("CLAUDE.md"\|"pronunciation-guide.md") |
| `get_reference` | Load reference file from `reference/` directory | `path` (e.g., "suno/prompt-guide.md") |
| `format_for_clipboard` | Format track/album for clipboard paste | `album_slug`, `track_slug` (optional), `format` ("markdown"\|"json") |

### TEXT ANALYSIS TOOLS (7) — Homographs, Pronunciations, Explicit Content

| Tool | Purpose | Parameters |
|------|---------|-----------|
| `check_homographs` | Find pronunciation-critical words (live, read, lead, etc.) | `album_slug`, `track_slug` |
| `scan_artist_names` | Find artist/band names that could be misinterpreted | `album_slug`, `track_slug` |
| `check_pronunciation_enforcement` | Verify phonetic spelling rules applied | `album_slug`, `track_slug` |
| `check_explicit_content` | Scan for explicit/profane words | `album_slug`, `track_slug` |
| `extract_links` | Extract all markdown links from track | `album_slug`, `track_slug` |
| `get_lyrics_stats` | Get word count, line count, syllable stats | `album_slug`, `track_slug` |
| `check_cross_track_repetition` | Find repeated phrases across album tracks | `album_slug` |

### LYRICS ANALYSIS TOOLS (5) — Rhymes, Readability, Distinctive Phrases

| Tool | Purpose | Parameters |
|------|---------|-----------|
| `extract_distinctive_phrases` | Find unique phrases (not common filler) | `album_slug`, `track_slug` |
| `count_syllables` | Count syllables in track lyrics | `album_slug`, `track_slug` |
| `analyze_readability` | Get readability metrics (Flesch-Kincaid, etc.) | `album_slug`, `track_slug` |
| `analyze_rhyme_scheme` | Analyze rhyme structure (AABB, ABAB, etc.) | `album_slug`, `track_slug` |
| `validate_section_structure` | Check for required sections (Verse, Chorus, etc.) | `album_slug`, `track_slug` |

### ALBUM OPERATIONS TOOLS (3) — Structure & Validation

| Tool | Purpose | Parameters |
|------|---------|-----------|
| `get_album_full` | Get complete album object (with all metadata) | `album_slug` |
| `validate_album_structure` | Check album directory layout correctness | `album_slug` |
| `create_album_structure` | Create album directory tree + template files | `album_slug`, `artist_name`, `genre` |

### GATES TOOLS (2) — Pre-Generation Verification

| Tool | Purpose | Parameters |
|------|---------|-----------|
| `run_pre_generation_gates` | Check source verification, explicit content, rhyme issues before Suno | `album_slug`, `track_slug` |
| `check_streaming_lyrics` | Verify lyrics suitable for Spotify/Apple without issues | `album_slug`, `track_slug` |

### STREAMING TOOLS (3) — Platform URLs

| Tool | Purpose | Parameters |
|------|---------|-----------|
| `get_streaming_urls` | Get Spotify, Apple Music, YouTube links for track | `album_slug`, `track_slug` |
| `update_streaming_url` | Update streaming URL in track frontmatter | `album_slug`, `track_slug`, `platform`, `url` |
| `verify_streaming_urls` | Verify all streaming URLs are valid/reachable | `album_slug` |

### SKILLS TOOLS (2) — Skills Metadata & Listing

| Tool | Purpose | Parameters |
|------|---------|-----------|
| `list_skills` | List all available skills with metadata | `filter_model` (optional, e.g., "opus", "sonnet"), `filter_tag` (optional) |
| `get_skill` | Get detailed skill information | `skill_name` (kebab-case) |

### STATUS TOOLS (2) — Album/Track State Transitions

| Tool | Purpose | Parameters |
|------|---------|-----------|
| `update_album_status` | Transition album to new status (Concept → Released) | `album_slug`, `new_status` |
| `create_track` | Create new track file in album with template | `album_slug`, `track_slug`, `title` |

### PROMO TOOLS (2) — Promo Directory & Content

| Tool | Purpose | Parameters |
|------|---------|-----------|
| `get_promo_status` | Check if promo directory exists and has content | `album_slug` |
| `get_promo_content` | Get promo copy (Twitter, Instagram, YouTube, etc.) | `album_slug`, `platform` (optional) |

### HEALTH TOOLS (2) — Plugin & Environment Checks

| Tool | Purpose | Parameters |
|------|---------|-----------|
| `get_plugin_version` | Get plugin version + last migration | None |
| `check_venv_health` | Check venv for missing/outdated packages | None |

### IDEAS TOOLS (2) — Album Idea Management

| Tool | Purpose | Parameters |
|------|---------|-----------|
| `create_idea` | Add new album idea to IDEAS.md | `title`, `genre`, `status` ("Pending"\|"In Progress") |
| `update_idea` | Update idea status and metadata | `title`, `new_status`, `genre` (optional) |

### RENAME TOOLS (2) — Album & Track Renaming

| Tool | Purpose | Parameters |
|------|---------|-----------|
| `rename_album` | Rename album (updates all paths, references) | `album_slug`, `new_name`, `new_slug` |
| `rename_track` | Rename track (updates slug, title, references) | `album_slug`, `track_slug`, `new_title`, `new_slug` |

### PROCESSING TOOLS (15) — Audio Mastering, Mixing, Promo Videos, Sheet Music

| Tool | Purpose | Parameters |
|------|---------|-----------|
| `analyze_audio` | Analyze audio for loudness, clipping, frequency balance | `album_slug`, `track_slug` |
| `qc_audio` | QC check track (headroom, distortion, phase issues) | `album_slug`, `track_slug` |
| `master_audio` | Master audio track (loudness normalization) | `album_slug`, `track_slug`, `target_lufs` (optional, default: -14) |
| `fix_dynamic_track` | Fix over-compressed/low-dynamic track | `album_slug`, `track_slug` |
| `master_with_reference` | Master audio using reference track as loudness model | `album_slug`, `track_slug`, `reference_track` |
| `transcribe_audio` | Transcribe audio to lyrics (speech-to-text) | `album_slug`, `track_slug` |
| `prepare_singles` | Prepare single versions (stems, artwork, metadata) | `album_slug` |
| `create_songbook` | Create PDF sheet music from track files | `album_slug` |
| `publish_sheet_music` | Publish sheet music to platforms | `album_slug` |
| `generate_promo_videos` | Generate 15s vertical promo videos for all tracks | `album_slug` |
| `generate_album_sampler` | Generate album sampler (mix of track clips) | `album_slug` |
| `master_album` | Master all album tracks (batch) | `album_slug`, `target_lufs` (optional) |
| `polish_audio` | Polish single track (noise reduction, EQ) | `album_slug`, `track_slug` |
| `analyze_mix_issues` | Detailed mix analysis (phase, EQ, dynamics) | `album_slug`, `track_slug` |
| `polish_album` | Polish all album tracks (batch) | `album_slug` |

### DATABASE TOOLS (8) — Tweet/Promo Management

| Tool | Purpose | Parameters |
|------|---------|-----------|
| `db_init` | Initialize PostgreSQL database + tables | None |
| `db_list_tweets` | List all stored tweets/promos | `limit` (optional) |
| `db_create_tweet` | Create new tweet entry | `album_slug`, `track_slug` (optional), `content`, `platform` |
| `db_update_tweet` | Update tweet content/status | `tweet_id`, `content`, `status` (optional) |
| `db_delete_tweet` | Delete tweet by ID | `tweet_id` |
| `db_search_tweets` | Search tweets by platform/album/content | `query`, `platform` (optional) |
| `db_sync_album` | Sync album tracks to database | `album_slug` |
| `db_get_tweet_stats` | Get tweet statistics (count by platform, status) | `album_slug` (optional) |

### MAINTENANCE TOOLS (3) — Legacy Cleanup, Mastering Resets, Audio Layout Migration

| Tool | Purpose | Parameters |
|------|---------|-----------|
| `reset_mastering` | Reset mastering status for tracks (re-master) | `album_slug` |
| `cleanup_legacy_venvs` | Remove old venv directories | `dry_run` (optional, preview only) |
| `migrate_audio_layout` | Migrate audio from old to new directory structure | `album_slug` |

---

## Python Tools by Category

### 1. STATE MANAGEMENT (`tools/state/`)

**Purpose:** Build and maintain the state cache from markdown source files.

**Files:**
- `indexer.py` — Main state builder; scans markdown, produces `~/.bitwize-music/cache/state.json`
- `parsers.py` — Parse album READMEs, track files, IDEAS.md, SKILL.md files
- `__main__.py` — CLI entry point for `python3 -m tools.state`

**Key Classes/Functions:**
- `build_state(config, plugin_root)` — Scan all markdown, return state dict
- `read_state()`, `write_state()` — Disk I/O for state cache
- `parse_album_readme()` — Extract title, status, release date, streaming URLs from frontmatter
- `parse_track_file()` — Extract track metadata, check for Suno links, lyric sections
- `parse_ideas_file()` — Parse IDEAS.md idea list
- `parse_skill_file()` — Extract skill metadata from SKILL.md

**CLI Commands:**
```
python3 tools/state/indexer.py rebuild    # Full scan
python3 tools/state/indexer.py update     # Incremental update
python3 tools/state/indexer.py validate   # Check state against schema
python3 tools/state/indexer.py show       # Pretty-print state summary
python3 tools/state/indexer.py session    # Update session data
```

**Schema Version:** 1.2.0 (see `reference/state-schema.md` for full schema)

---

### 2. SHARED UTILITIES (`tools/shared/`)

**Purpose:** Common utilities used by all tools and MCP server.

**Files:**
- `config.py` — Load `~/.bitwize-music/config.yaml`, validate overrides
- `paths.py` — Resolve content/audio/documents paths (mirrored structure)
- `logging_config.py` — Setup logging (file + stderr, debug mode)
- `progress.py` — Progress bar for long-running operations
- `colors.py` — ANSI color codes for terminal output
- `text_utils.py` — Text processing (slugification, tokenization)
- `media_utils.py` — Audio/video utilities (ffmpeg detection, dominant color extraction)
- `fonts.py` — Font detection for promo videos (system fonts)

**Key Constants:**
- `CONFIG_PATH = Path.home() / ".bitwize-music" / "config.yaml"`
- `OVERRIDE_FILES` — Known override files and validation rules

---

### 3. AUDIO MASTERING (`tools/mastering/`)

**Purpose:** Professional audio mastering and loudness normalization.

**Files:**
- `master_tracks.py` — Main mastering script (loudness normalization, EQ, limiting)
- `qc_tracks.py` — QC analysis (loudness, clipping, phase issues)
- `analyze_tracks.py` — Detailed audio analysis (frequency response, dynamic range)
- `reference_master.py` — Master using reference track as loudness model
- `fix_dynamic_track.py` — Fix over-compressed tracks

**Dependencies:**
- `pyloudnorm` — ITU-R BS.1770-4 loudness metering
- `soundfile` — WAV read/write
- `scipy` — DSP (signal processing, filtering)
- `numpy` — Numeric arrays
- `matchering` — Audio matching/mastering presets

**Targets:**
- Streaming default: -14 LUFS
- Broadcast: -23 LUFS
- Loud/Aggressive: -10 LUFS
- Accessible for hearing impaired: -8 LUFS

**Genre Presets:** Load from `mastering/genre-presets.yaml` (target LUFS, EQ cuts by genre)

---

### 4. AUDIO MIXING & POLISH (`tools/mixing/`)

**Purpose:** Mix polishing and noise reduction.

**Files:**
- `mix_tracks.py` — Gentle compression, noise gate, frequency balancing

**Dependencies:**
- `noisereduce` — AI-based noise reduction
- `scipy`, `numpy`, `soundfile` — Audio processing

---

### 5. SHEET MUSIC (`tools/sheet-music/`)

**Purpose:** Generate PDF sheet music from lyrics.

**Files:**
- `create_songbook.py` — Create PDF songbook from tracks
- `prepare_singles.py` — Prepare single versions with artwork/metadata
- `transcribe.py` — Speech-to-text transcription from audio

**Dependencies:**
- `reportlab` — PDF generation
- `pypdf` — PDF manipulation
- `librosa` — Audio feature extraction (for transcription)

**Integrations:**
- AnthemScore (if available) for automatic notation generation

---

### 6. PROMO VIDEOS & PROMOTION (`tools/promotion/`)

**Purpose:** Generate promo videos and sampler tracks.

**Files:**
- `generate_promo_video.py` — Create 15s vertical videos (9:16 aspect ratio)
- `generate_album_sampler.py` — Create album sampler (mix of track clips)
- `generate_all_promos.py` — Batch generate all promos for album

**Dependencies:**
- `ffmpeg` — Video encoding, audio mixing
- `pillow` — Image processing (artwork)
- `librosa` — Audio feature extraction

**Video Specs:**
- Format: 9:16 (vertical, mobile-friendly)
- Resolution: 1080x1920
- Duration: 15s default
- Output: MP4 (H.264 video, AAC audio)

**Features:**
- Waveform visualization
- Album artwork overlay
- Text overlay (track name, artist)
- Color schemes (from dominant artwork color)
- Batch processing with thread pool

---

### 7. CLOUD UPLOADS (`tools/cloud/`)

**Purpose:** Upload promo videos and album content to cloud storage.

**Files:**
- `upload_to_cloud.py` — Upload to Cloudflare R2 or AWS S3 via boto3

**Dependencies:**
- `boto3` — AWS SDK (S3 API)

**Providers:**
- **Cloudflare R2** — S3-compatible API, configured in config.yaml
- **AWS S3** — Direct S3 API

**Configuration:**
```yaml
cloud:
  provider: "r2"  # or "s3"
  r2:
    account_id: "..."
    access_key_id: "..."
    secret_access_key: "..."
    bucket: "..."
    public_domain: "..."
```

---

### 8. DATABASE (`tools/database/`)

**Purpose:** PostgreSQL integration for tweet/promo management.

**Files:**
- `connection.py` — PostgreSQL connection pooling
- `__init__.py` — Database operations (CRUD for tweets/promos)

**Dependencies:**
- `psycopg2-binary` — PostgreSQL adapter

**Tables:**
- `tweets` — Platform, content, status, timestamps
- `album_sync` — Album → track mapping

---

### 9. N8N INTEGRATION (`tools/n8n/`)

**Purpose:** (Currently empty placeholder)

Future integration with n8n workflow automation platform.

---

### 10. USERSCRIPTS (`tools/userscripts/`)

**Purpose:** (Currently empty)

Extensible user-defined scripts.

---

## Dependencies & External Requirements

### Python Dependencies

**Core (required for all):**
```
mcp[cli]==1.26.0        # MCP SDK
pyyaml==6.0.3           # Config parsing
```

**Audio Mastering:**
```
matchering==2.0.6       # Mastering presets + matching
pyloudnorm==0.2.0       # ITU-R BS.1770-4 loudness metering
scipy==1.17.1           # Signal processing (DSP)
numpy==2.4.3            # Numeric arrays
soundfile==0.13.1       # WAV I/O
```

**Audio Mixing:**
```
noisereduce==3.0.3      # Noise reduction
```

**Promo Videos:**
```
pillow==12.1.1          # Image processing
librosa==0.11.0         # Audio analysis
```

**Sheet Music:**
```
pypdf==6.9.2            # PDF manipulation
reportlab==4.4.10       # PDF generation
```

**Cloud Uploads:**
```
boto3==1.42.77          # AWS SDK (S3)
```

**Database:**
```
psycopg2-binary==2.9.11 # PostgreSQL adapter
```

**Browser Automation (optional):**
```
playwright==1.58.0      # Browser automation (for document-hunter skill)
  + playwright install chromium  # After pip install
```

### External Requirements

| Tool | Purpose | Installation |
|------|---------|--------------|
| **ffmpeg** | Audio/video encoding, mixing | `brew install ffmpeg` (macOS), `apt-get install ffmpeg` (Linux) |
| **Python 3.10+** | Runtime | Required for type hints, async/await |
| **PostgreSQL** (optional) | Tweet/promo database | Needed only for db_* tools |
| **Chromium/Playwright** (optional) | Web scraping | `playwright install chromium` after pip install |
| **AnthemScore** (optional) | Automatic sheet music | Download from anthem.ai (commercial) |

---

## State Management Schema

### State Cache File Location

`~/.bitwize-music/cache/state.json`

### Root Structure

```json
{
  "version": "1.2.0",
  "generated_at": "2026-03-28T17:30:00+00:00",
  "plugin_version": "0.43.1",
  "config": { /* resolved config snapshot */ },
  "albums": { /* album slug → album data */ },
  "ideas": { /* album ideas */ },
  "skills": { /* indexed skill metadata */ },
  "session": { /* session context for resume */ }
}
```

### Album Data Structure

```json
{
  "slug": "my-album",
  "path": "/absolute/path/to/album",
  "genre": "hiphop",
  "title": "My Album Title",
  "status": "In Progress",
  "explicit": false,
  "release_date": null,
  "track_count": 10,
  "tracks_completed": 3,
  "streaming_urls": {
    "spotify": "https://open.spotify.com/album/...",
    "apple": "https://music.apple.com/..."
  },
  "readme_mtime": 1711638600.0,
  "tracks": {
    "01-track-one": { /* track data */ },
    "02-track-two": { /* track data */ }
  }
}
```

### Track Data Structure

```json
{
  "slug": "01-track-one",
  "path": "/absolute/path/to/01-track-one.md",
  "title": "Track One",
  "status": "In Progress",
  "explicit": false,
  "has_suno_link": true,
  "sources_verified": "Pending",
  "mtime": 1711638600.0
}
```

### Album Status Values

Valid transitions (enforced by `status.py`):
```
Concept → Research Complete → Sources Verified → In Progress → Complete → Released
```

### Track Status Values

Valid values:
```
Not Started → Sources Pending → Sources Verified → In Progress → Generated → Final
```

**Transition Rules:**
- Album can only advance if ALL tracks reach corresponding level
- Track status is independent but album status reflects minimum track status
- Source verification is a prerequisite gate before generation

### Session Context

```json
{
  "last_album": "my-album",
  "last_track": "03-track-three",
  "last_phase": "Writing",
  "pending_actions": [
    "Verify sources for track 3",
    "Write hook for chorus"
  ],
  "updated_at": "2026-03-28T17:30:00+00:00"
}
```

### Staleness Detection

The MCP server automatically detects when cache is stale:

1. **File mtime comparison** — Compare cached mtime vs current file mtime
2. **Config change detection** — Monitor `config.yaml` for changes
3. **Auto-rebuild trigger** — If stale, reload or rebuild automatically
4. **Version migration** — On schema version change, auto-migrate if same major version

---

## Setup Requirements for New Users

### Quick Start (5 minutes)

1. **Create venv:**
   ```bash
   python3 -m venv ~/.bitwize-music/venv
   ```

2. **Install dependencies:**
   ```bash
   ~/.bitwize-music/venv/bin/pip install -r requirements.txt
   ```

3. **Install browser (optional, for document-hunter):**
   ```bash
   ~/.bitwize-music/venv/bin/playwright install chromium
   ```

4. **Create config:**
   ```bash
   cp config/config.example.yaml ~/.bitwize-music/config.yaml
   ```

5. **Initialize state cache:**
   ```bash
   ~/.bitwize-music/venv/bin/python3 tools/state/indexer.py rebuild
   ```

### Verification

**Check MCP server is ready:**
```bash
~/.bitwize-music/venv/bin/python3 -c "import mcp; print('✅ MCP ready')"
```

**Check venv health:**
```bash
~/.bitwize-music/venv/bin/python3 -c "from tools.state.indexer import *; print('✅ Tools ready')"
```

**Check dependencies:**
```bash
# For mastering:
~/.bitwize-music/venv/bin/python3 -c "import pyloudnorm, scipy, soundfile; print('✅ Mastering ready')"

# For promo videos:
~/.bitwize-music/venv/bin/python3 -c "import pillow, librosa; print('✅ Promo videos ready')"

# For cloud uploads:
~/.bitwize-music/venv/bin/python3 -c "import boto3; print('✅ Cloud uploads ready')"

# For database:
~/.bitwize-music/venv/bin/python3 -c "import psycopg2; print('✅ Database ready')"
```

### Required External Tools

```bash
# Check ffmpeg:
ffmpeg -version

# Install ffmpeg:
# macOS:
brew install ffmpeg

# Linux (Ubuntu/Debian):
sudo apt-get install ffmpeg

# Linux (Fedora):
sudo dnf install ffmpeg
```

### Configuration Structure

**File:** `~/.bitwize-music/config.yaml`

**Required sections:**
```yaml
artist:
  name: "Your Artist Name"

paths:
  content_root: /path/to/content
  audio_root: /path/to/audio
  documents_root: /path/to/documents
  overrides: /path/to/overrides  # Optional
```

**Optional sections:**
```yaml
debug:
  logging: true  # Enable file-based debug logging

cloud:
  provider: "r2"  # or "s3"
  r2:
    account_id: "..."
    access_key_id: "..."
    secret_access_key: "..."

database:
  host: localhost
  port: 5432
  name: bitwize_music
  user: postgres
  password: "..."
```

### Upgrade Path

**Version detection:**
```python
from tools.state.indexer import CURRENT_VERSION
print(f"State schema version: {CURRENT_VERSION}")
```

**Auto-rebuild on version mismatch:**
- Happens automatically when state is loaded if schema version differs
- Session data is preserved during rebuild
- No user action required

**Manual rebuild:**
```bash
~/.bitwize-music/venv/bin/python3 tools/state/indexer.py rebuild
```

---

## Key Design Principles

### 1. Markdown as Source of Truth
- All content (albums, tracks, ideas, skills) lives in markdown files in git
- State cache is disposable and always regenerated from markdown
- No database lock-in; raw markdown is human-readable and portable

### 2. Mirrored Path Structure
- **Content:** `{content_root}/artists/[artist]/albums/[genre]/[album]/`
- **Audio:** `{audio_root}/artists/[artist]/albums/[genre]/[album]/`
- **Documents:** `{documents_root}/artists/[artist]/albums/[genre]/[album]/`
- Enables separation of concerns (git-tracked content vs. audio files vs. PDFs)

### 3. Thread-Safe State Management
- StateCache uses read-write locks for concurrent access
- Session updates are atomic (read-modify-write under lock)
- Safe for concurrent MCP tool calls from Claude Code

### 4. Zero Config Assumptions
- All paths are explicit (no hidden defaults)
- Missing config exits with helpful error
- Overrides are optional (fail silently if missing)

### 5. Gradual Capability Loading
- Detect ffmpeg, db, cloud credentials at runtime
- Graceful degradation if optional dependencies missing
- Helpful error messages for missing external tools

---

## Performance Characteristics

### State Cache Load Time

| Scenario | Time |
|----------|------|
| Cache hit (no reload) | <1ms |
| Load from disk | 50-200ms (depends on album count) |
| Full rebuild (50 albums) | 1-3 seconds |
| Full rebuild (500 albums) | 5-15 seconds |

### Audio Processing Time

| Task | Time |
|------|------|
| Analyze audio (2min track) | 2-5 seconds |
| Master audio (2min track) | 3-8 seconds |
| Generate promo video (15s) | 5-15 seconds |
| Batch master (10 tracks) | 30-80 seconds (parallel) |

### MCP Tool Response Time

| Tool | Time |
|------|------|
| Album/track queries | <10ms |
| Search (all albums) | 10-50ms |
| State rebuild trigger | 1-3s |
| Pre-generation gates check | 100-500ms |

---

## Testing

### Test Structure

```
tests/
├── unit/                          # Unit tests (no dependencies)
│   ├── shared/                    # Config, paths, logging
│   ├── state/                     # State indexer + parsers
│   ├── mastering/                 # Mastering scripts
│   ├── mixing/                    # Mixing scripts
│   ├── sheet_music/               # Sheet music generation
│   ├── promotion/                 # Promo video generation
│   ├── cloud/                     # Cloud upload utilities
│   └── database/                  # Database operations
├── plugin/                        # Plugin validation tests
└── fixtures/                      # Test data (mock albums, etc.)
```

### Running Tests

```bash
# All tests:
pytest

# Specific category:
pytest tests/unit/mastering/

# With coverage:
pytest --cov=tools --cov=handlers

# Slow tests (5+ seconds):
pytest -m slow

# Plugin validation only:
pytest -m plugin
```

### Markers

- `@pytest.mark.unit` — Fast (<1s)
- `@pytest.mark.slow` — Slow (>5s)
- `@pytest.mark.plugin` — Plugin validation
- `@pytest.mark.integration` — Cross-module integration

---

## Extensibility

### Adding a New MCP Tool

1. **Create function in handler module:**
   ```python
   # handlers/my_handler.py
   async def my_tool(param1: str, param2: int = 0) -> str:
       """Short description."""
       return json.dumps({"result": "value"})
   ```

2. **Register in handler's `register()` function:**
   ```python
   def register(mcp: Any) -> None:
       mcp.tool()(my_tool)
   ```

3. **Import in `server.py`:**
   ```python
   from handlers import my_handler
   my_handler.register(mcp)
   ```

### Adding a New Python Tool Module

1. **Create directory:** `tools/my_module/`
2. **Add `__init__.py` and implementation files**
3. **Update `requirements.txt` with dependencies**
4. **Add tests to `tests/unit/my_module/`**

### Adding Tests

```python
# tests/unit/my_module/test_my_script.py
import pytest
from tools.my_module.my_script import my_function

@pytest.mark.unit
def test_my_function():
    result = my_function("input")
    assert result == "expected"
```

---

## Summary

The MCP server provides a **unified interface** for all music production tasks, exposing 70+ tools organized into 14 handler modules. The Python tools ecosystem handles specialized tasks like audio mastering, promo generation, and state management. Thread-safe state caching with automatic staleness detection ensures responsive performance. The architecture prioritizes **markdown as source of truth**, **zero hidden assumptions**, and **graceful degradation** when optional dependencies are missing.

**Total Line Count:**
- MCP server: ~3,500 LOC
- Handler modules: ~10,000 LOC
- Python tools: ~15,000 LOC
- Tests: ~4,000 LOC
