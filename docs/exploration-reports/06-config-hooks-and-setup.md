# Getting Started: Setup, Configuration & Infrastructure Guide

This guide covers the complete setup process for the bitwize-music Claude Code plugin — from initial installation through first use. Read this **first** when setting up your own instance.

---

## Quick Start (5 minutes)

If you just want to get going:

```bash
# 1. Install via marketplace
/plugin marketplace add bitwize-music-studio/claude-ai-music-skills
/plugin install bitwize-music@claude-ai-music-skills

# 2. Set up dependencies
/bitwize-music:setup

# 3. Configure workspace
/bitwize-music:configure

# 4. Start your first album
/bitwize-music:tutorial new-album
```

---

## Part 1: Installation Methods

### Method A: Marketplace Install (Recommended for Users)

Use this if you're installing the plugin from the Claude Code marketplace.

```bash
# Add marketplace source
/plugin marketplace add bitwize-music-studio/claude-ai-music-skills

# Install plugin
/plugin install bitwize-music@claude-ai-music-skills

# Verify installation
/plugin status
```

**What it does**: Downloads plugin from GitHub releases into `~/.claude/plugins/bitwize-music/`.

### Method B: Clone Install (Recommended for Development)

Use this if you're contributing to the project or want the absolute latest code.

```bash
# Clone repository
git clone https://github.com/bitwize-music-studio/claude-ai-music-skills.git
cd claude-ai-music-skills

# Start Claude Code
claude

# Verify plugin loads
/plugin status
```

**What it does**: Claude Code automatically detects and loads the plugin from the cloned directory via `CLAUDE.md` in the root.

### Method C: Manual Plugin Directory

Advanced: copy the plugin manually.

```bash
mkdir -p ~/.claude/plugins/bitwize-music
cp -r . ~/.claude/plugins/bitwize-music/
```

---

## Part 2: Python Environment & Dependencies

The plugin uses a **unified venv approach** — a single Python environment shared across all tools.

### Where Everything Lives

```
~/.bitwize-music/                    # Tools root (persistent, survives plugin updates)
├── config.yaml                      # Your configuration (user-maintained)
├── venv/                            # Python 3.10+ virtual environment
│   ├── bin/python3                  # Python executable
│   └── lib/python3.*/site-packages/ # Dependencies
├── cache/                           # State cache (auto-managed)
│   └── state.json                   # Album/track state
└── logs/                            # Debug logs (if enabled)
```

### Environment Detection & Installation

Run the setup skill to detect your Python environment and install dependencies:

```bash
/bitwize-music:setup
```

This will:
1. **Detect** your Python version and platform
2. **Check** for existing venv and component status
3. **Report** what needs to be installed
4. **Guide** you through installation

### Manual Installation (if needed)

```bash
# Step 1: Create unified venv
python3 -m venv ~/.bitwize-music/venv

# Step 2: Install all dependencies
~/.bitwize-music/venv/bin/pip install -r /path/to/plugin/requirements.txt

# Step 3: Set up browser for document-hunter (optional)
~/.bitwize-music/venv/bin/playwright install chromium
```

### What Gets Installed

The `requirements.txt` includes:

- **mcp** - MCP server communication
- **pyyaml** - Configuration file parsing
- **matchering** - Audio reference matching for mastering
- **pyloudnorm** - LUFS loudness analysis
- **scipy, numpy, soundfile** - Audio processing
- **boto3, botocore** - AWS S3 cloud uploads
- **requests, beautifulsoup4, playwright** - Web scraping (document-hunter)
- **psycopg2-binary** - PostgreSQL database (optional)

All installed in `~/.bitwize-music/venv` — works on Linux, macOS, Windows (WSL).

### Troubleshooting Python Setup

| Issue | Solution |
|-------|----------|
| "Python 3.8 only" | `apt install python3.10` or brew upgrade Python |
| "venv not supported" | Some Linux distros: `apt install python3-venv` |
| "Externally managed Python" | Use the unified venv approach (it's designed for this) |
| "Permission denied ~/.bitwize-music" | `mkdir -p ~/.bitwize-music && chmod 755 ~/.bitwize-music` |
| "Import errors after install" | Restart Claude Code or run: `~/.bitwize-music/venv/bin/pip install -r requirements.txt --force-reinstall` |

---

## Part 3: Configuration File

Configuration lives at `~/.bitwize-music/config.yaml` — **outside the plugin directory**. This means:
- Updates don't overwrite your settings
- Same config whether you cloned or installed via marketplace
- Easy to back up and version control separately

### Creating Your Config

#### Interactive Setup (Recommended)

```bash
/bitwize-music:configure
```

This walks you through creating/editing the config interactively with prompts.

#### Manual Setup

```bash
# Create directory
mkdir -p ~/.bitwize-music

# Copy example
cp /path/to/plugin/config/config.example.yaml ~/.bitwize-music/config.yaml

# Edit with your values
nano ~/.bitwize-music/config.yaml
```

### Required Configuration

Three fields are **required**:

```yaml
artist:
  name: "your-artist-name"        # Used in all file paths

paths:
  content_root: "~/music-projects"      # Where albums and research live
  audio_root: "~/music-projects/audio"  # Where mastered audio goes
  documents_root: "~/music-projects/documents"  # Where PDFs go
```

**Important path notes:**
- All paths support `~` for home directory
- Use lowercase-with-hyphens for artist names: `my-band`, not `My Band`
- Paths are mirrored: `{root}/artists/[artist]/albums/[genre]/[album]/`
- Audio and documents include the artist folder after the root

### Optional Configuration Sections

#### Artist Info

```yaml
artist:
  name: "your-artist-name"
  genres:
    - "electronic"
    - "hip-hop"
  style: "dark industrial electronic with aggressive vocals"
```

#### Platform URLs

```yaml
urls:
  soundcloud: "https://soundcloud.com/your-artist"
  spotify: "https://open.spotify.com/artist/your-id"
  bandcamp: "https://your-artist.bandcamp.com"
  youtube: "https://youtube.com/@your-artist"
  twitter: "https://x.com/your-artist"
```

#### Generation Service

```yaml
generation:
  service: suno                          # (default, currently only option)
  max_lyric_words: 800                   # Suno V5 practical limit
  require_suno_link_for_final: true      # Block Final status without Suno link
  require_source_path_for_documentary: true  # Enforce research verification
```

#### Promo Videos

```yaml
promotion:
  default_style: "pulse"                 # pulse, bars, line, mirror, neon, etc.
  duration: 15                           # seconds
  include_sampler: true                  # Generate album sampler video
  sampler_clip_duration: 12              # seconds per track in sampler
```

#### Sheet Music

```yaml
sheet_music:
  enabled: true
  page_size: "letter"                    # letter, 9x12, 6x9
  section_headers: false                 # Include [Verse], [Chorus] labels
```

#### Cloud Storage (Optional)

```yaml
cloud:
  enabled: true
  provider: "r2"                         # or "s3"
  public_read: true                      # Make uploads publicly accessible
  r2:
    account_id: "your-account-id"
    access_key_id: "your-key"
    secret_access_key: "your-secret"
    bucket: "promo-videos"
```

#### Database (Optional)

```yaml
database:
  enabled: true
  host: "localhost"
  port: 5432
  name: "your-database"
  user: "your-username"
  password: "your-password"
```

#### Logging (Optional)

```yaml
logging:
  enabled: true
  level: "debug"                         # debug, info, warning, error
  file: "~/.bitwize-music/logs/debug.log"
  max_size_mb: 5
  backup_count: 3
```

### Common Configuration Patterns

**Pattern 1: Minimal (Getting Started)**
```yaml
artist:
  name: "test-artist"

paths:
  content_root: "~/music-projects"
  audio_root: "~/music-projects/audio"
  documents_root: "~/music-projects/documents"
```

**Pattern 2: Full Featured (Power User)**
```yaml
artist:
  name: "bitwize"
  genres: ["electronic", "hip-hop"]
  style: "dark industrial electronic"

paths:
  content_root: "~/bitwize-music"
  audio_root: "~/bitwize-music/audio"
  documents_root: "~/bitwize-music/documents"
  overrides: "~/bitwize-music/overrides"
  ideas_file: "~/bitwize-music/IDEAS.md"

urls:
  soundcloud: "https://soundcloud.com/bitwize"
  spotify: "https://open.spotify.com/artist/abc123"

generation:
  service: suno

promotion:
  default_style: "neon"
  duration: 15
  include_sampler: true

sheet_music:
  page_size: "9x12"
```

**Pattern 3: macOS Standard Paths**
```yaml
artist:
  name: "your-artist"

paths:
  content_root: "~/Music/ai-music-projects"
  audio_root: "~/Music/ai-music-projects/mastered"
  documents_root: "~/Documents/ai-music-research"
```

**Pattern 4: WSL with Windows Paths**
```yaml
artist:
  name: "your-artist"

paths:
  content_root: "/mnt/c/Users/YourName/Music/ai-music"
  audio_root: "/mnt/c/Users/YourName/Music/ai-music/audio"
  documents_root: "/mnt/c/Users/YourName/Documents/ai-music-docs"
```

### Path Structure Explained

The plugin maintains a **mirrored path structure** across three roots:

```
{content_root}/artists/[artist]/albums/[genre]/[album]/
  ├── README.md                    # Album concept
  ├── SOURCES.md                   # Citations (documentary albums)
  ├── RESEARCH.md                  # Research notes
  └── tracks/
      ├── 01-track-name.md         # Track lyrics and metadata
      └── 02-track-name.md

{audio_root}/artists/[artist]/albums/[genre]/[album]/
  ├── album.png                    # Album artwork
  ├── 01-track-name.wav            # Mastered audio
  └── 02-track-name.wav

{documents_root}/artists/[artist]/albums/[genre]/[album]/
  ├── source-1.pdf
  ├── source-2.pdf
  └── manifest.json                # Document metadata
```

**Common mistake**: Using flat paths like `{audio_root}/my-album/`. Must include the full artist/genre structure!

---

## Part 4: Overrides System

The overrides directory lets you customize skills without plugin updates overwriting your preferences.

### How It Works

1. Each skill checks for its override file in `{paths.overrides}/`
2. If found, merges with base (or replaces, depending on skill)
3. If not found, uses base only (no error)
4. Can be committed to your music content repo

### Creating Overrides

Default location: `{content_root}/overrides/` (configurable as `paths.overrides`)

```bash
# Create overrides directory
mkdir -p ~/music-projects/overrides

# Copy examples from plugin (optional)
cp plugin/config/overrides.example/* ~/music-projects/overrides/
```

### Available Override Files

| File | Purpose | Used By |
|------|---------|---------|
| `CLAUDE.md` | Custom workflow instructions | All skills |
| `pronunciation-guide.md` | Artist/character name pronunciations | Lyric writer, pronunciation specialist |
| `explicit-words.md` | Custom explicit content words | Explicit checker |
| `lyric-writing-guide.md` | Style preferences for lyrics | Lyric writer |
| `suno-preferences.md` | Suno genre mappings and defaults | Suno engineer |
| `mastering-presets.yaml` | Custom EQ/loudness presets | Mastering engineer |
| `album-planning-guide.md` | Album conceptualization preferences | Album conceptualizer |
| `album-art-preferences.md` | Visual style guidelines | Album art director |
| `research-preferences.md` | Research depth and verification standards | Researcher |
| `release-preferences.md` | QA requirements and platform priorities | Release director |
| `promotion-preferences.md` | Promo video and copy style | Promo writer, promo director |
| `sheet-music-preferences.md` | Page layout and formatting | Sheet music publisher |

### Example Override Files

**`pronunciation-guide.md`** - Add artist/character-specific pronunciations:

```markdown
# Custom Pronunciation Guide

## Artist-Specific Terms
| Word | Phonetic | Notes |
|------|----------|-------|
| BitWize | Bit-Wize | Artist name |
| Larocca | Luh-rock-uh | Character name |

## Album-Specific Words
| Word | Phonetic | Notes |
|------|----------|-------|
| Finnerty | Finn-er-tee | Character name |
```

**`suno-preferences.md`** - Customize Suno settings:

```markdown
# Suno Preferences

## Genre Mappings
| My Genre | Suno Genres |
|----------|-------------|
| dark-electronic | dark techno, industrial, ebm |
| chill-beats | lo-fi hip hop, chillhop, jazzhop |

## Default Settings
- Instrumental: false
- Model: V5
```

**`mastering-presets.yaml`** - Custom mastering EQ:

```yaml
genres:
  dark-electronic:
    cut_highmid: -3
    boost_sub: 2
    target_lufs: -12

  ambient:
    cut_highmid: -1
    boost_sub: 0
    target_lufs: -16
```

---

## Part 5: Hooks & Quality Gates

The plugin includes automated validation hooks to catch errors early.

### Pre-Commit Hook

Install this to validate changes before committing:

```bash
# Automatic install
bash hooks/install.sh

# Or manual install
cp hooks/pre-commit .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

**Runs 11 checks:**
1. Ruff linter (Python code style)
2. JSON/YAML validation
3. CLAUDE.md size limit (40K characters)
4. Version sync (plugin.json ↔ marketplace.json)
5. Skill frontmatter validation
6. CHANGELOG format check
7. Merge conflict detection
8. Large file check (>500KB)
9. Security scan (bandit + pip-audit)
10. Unit tests (pytest)
11. Plugin tests

**Bypass if needed** (not recommended):
```bash
git commit --no-verify
```

### Post-Tool Hooks

Run automatically when you write/edit files in Claude Code:

**Validation**:
```bash
python3 ${CLAUDE_PLUGIN_ROOT}/hooks/validate_track.py
python3 ${CLAUDE_PLUGIN_ROOT}/hooks/check_version_sync.py
```

These hooks validate track structure and ensure version files stay in sync.

---

## Part 6: Session Start Workflow

On every session, Claude Code runs a 8-step initialization process:

### Step 1: Verify Setup
```bash
~/.bitwize-music/venv/bin/python3 -c "import mcp"
```
- ✅ MCP ready → continue
- ❌ MCP missing → suggest `/bitwize-music:setup mcp`

### Step 2: Check venv Health
Compares installed versions vs `requirements.txt`:
- ✅ All match → continue silently
- ⚠️ Stale → warn and provide fix command
- ❌ No venv → stop and suggest `/bitwize-music:setup`

### Step 3: Load Configuration
Reads `~/.bitwize-music/config.yaml`:
- ✅ Exists → load and validate
- ❌ Missing → suggest `/bitwize-music:configure`

### Step 4: Load Overrides
Checks for user-provided customizations:
- `{overrides}/CLAUDE.md` → merge instructions
- `{overrides}/pronunciation-guide.md` → merge pronunciations
- Missing files → skip silently (optional)

### Step 5: Load State via MCP
Uses MCP server to query:
- `get_config` → verify config loaded
- `list_albums` → get album statuses
- `get_ideas` → count album ideas
- `get_pending_verifications` → check source verification status
- `get_session` → resume last session context

### Step 6: Check for Plugin Upgrades
Compares `plugin_version` in `state.json` vs `.claude-plugin/plugin.json`:
- First run → set version, skip migrations
- Upgrade detected → read `migrations/` for applicable versions
- Current → no action

### Step 7: Report Status
Shows:
- Venv health warnings (if any)
- Album ideas count
- In-progress albums
- Pending verifications
- Last session context
- Contextual tips (resume, research, pronunciation, etc.)

### Step 8: Ask Next Action
"What would you like to work on?"

---

## Part 7: Plugin Architecture

### Directory Structure

```
claude-ai-music-skills/
├── CLAUDE.md                       # Session start and workflow rules
├── .claude-plugin/
│   ├── plugin.json                 # Plugin manifest
│   └── marketplace.json            # Marketplace metadata
├── skills/
│   ├── setup/                      # Environment setup wizard
│   ├── configure/                  # Configuration management
│   ├── tutorial/                   # First-time user guide
│   ├── lyric-writer/               # Lyric composition and QC
│   ├── album-conceptualizer/       # 7-phase album planning
│   ├── researcher/                 # Research coordination
│   ├── mastering-engineer/         # Audio mastering
│   ├── release-director/           # Release workflow
│   └── [19 more skills...]         # See /reference/SKILL_INDEX.md
├── servers/
│   └── bitwize-music-server/       # MCP server (state management)
├── tools/
│   ├── mastering/                  # Audio analysis and mastering scripts
│   ├── mixing/                     # Stem processing and polishing
│   ├── promotion/                  # Promo video generation
│   ├── sheet-music/                # Sheet music conversion
│   ├── cloud/                      # Cloud storage integration
│   ├── database/                   # Social media database
│   └── state/                      # State cache indexing
├── templates/
│   ├── album.md                    # Album concept template
│   ├── track.md                    # Track metadata template
│   ├── artist.md                   # Artist profile template
│   ├── research.md                 # Research notes template
│   ├── sources.md                  # Sources/citations template
│   └── promo/                      # Social media templates
├── config/
│   ├── config.example.yaml         # Configuration template
│   └── overrides.example/          # Override file examples
├── hooks/
│   ├── pre-commit                  # Git pre-commit validations
│   └── validate_track.py           # Track structure validator
├── migrations/
│   ├── 0.40.0.md                   # Upgrade migration guides
│   ├── 0.43.0.md
│   └── README.md                   # How migrations work
├── .github/
│   ├── workflows/                  # CI/CD automation
│   └── ISSUE_TEMPLATE/             # Issue templates
└── tests/                          # Test suite
```

### MCP Server

The MCP server (`servers/bitwize-music-server/`) manages all state:

Runs in: `~/.bitwize-music/venv/bin/python3`

**Provides tools for:**
- `get_config` - Read configuration
- `list_albums` - List all albums with status
- `find_album` - Find specific album
- `get_track` - Get track metadata
- `list_skills` - List available skills
- `get_skill` - Get skill details
- `get_ideas` - Get album ideas
- `get_pending_verifications` - Get source verification status
- `update_session` - Save session context
- `rebuild_state` - Rebuild state cache from files
- `check_venv_health` - Check Python environment health

**Storage**: `~/.bitwize-music/cache/state.json` (auto-generated, rebuilt on demand)

### Skills

23 specialized skills covering the full workflow:

- **Setup & Config**: setup, configure, tutorial
- **Concept**: album-conceptualizer
- **Lyrics**: lyric-writer, lyric-reviewer, pronunciation-specialist, explicit-checker
- **Research**: researcher, document-hunter, verify-sources
- **Suno**: suno-engineer
- **Audio**: mixing-engineer, mastering-engineer
- **Promotion**: promo-director, album-art-director
- **Release**: release-director, social-media-manager
- **Utilities**: plagiarism-checker, voice-checker, skill-model-updater

See `/reference/SKILL_INDEX.md` for full index and decision tree.

---

## Part 8: Testing & Validation

### Quick Verification

```bash
# Verify setup
/bitwize-music:setup

# Verify configuration
/bitwize-music:configure show

# Test a skill
/bitwize-music:tutorial help
```

### Unit Tests

```bash
# Run all tests
pytest tests/

# Run specific test
pytest tests/unit/test_mastering.py

# With coverage
pytest --cov=tools tests/
```

### Full Testing Plan

See `TESTING.md` for comprehensive testing checklist including:
- Fresh install testing (marketplace + clone methods)
- Configuration testing and path resolution
- Core workflow testing
- Research workflow testing
- Mastering workflow testing
- Git safety testing
- Skill inventory checks
- Edge cases
- End-to-end workflow
- Cleanup procedures

---

## Part 9: First-Time Workflow

After installation and configuration:

### Step 1: Run Setup
```bash
/bitwize-music:setup
```

Detects environment and installs dependencies.

### Step 2: Configure Workspace
```bash
/bitwize-music:configure
```

Creates config with artist name and paths.

### Step 3: Start Tutorial
```bash
/bitwize-music:tutorial help
```

Learn the basic commands.

### Step 4: Create First Album
```bash
/bitwize-music:new-album
```

Walk through 7-phase album conceptualization (you can skip to phase 2 for quick start).

### Step 5: Write Lyrics
```bash
/bitwize-music:lyric-writer {track-path}
```

Write a track, get automatic QC feedback.

### Step 6: Generate Suno Prompt
```bash
/bitwize-music:suno-engineer
```

Get a Suno V5-ready style prompt and lyrics box.

### Step 7: Generate and Master
- Generate on Suno.com
- Download WAV
- Import to plugin
- Run mastering

### Step 8: Release
```bash
/bitwize-music:release-director
```

Release workflow: metadata, social copy, streaming platforms.

---

## Part 10: Troubleshooting

### Config Not Found

```
Error: Config not found
```

**Solution**:
```bash
mkdir -p ~/.bitwize-music
cp config/config.example.yaml ~/.bitwize-music/config.yaml
# Edit config.yaml with your settings
```

### MCP Server Not Starting

```
Error: MCP server connection failed
```

**Solutions**:
1. Check venv: `ls ~/.bitwize-music/venv/bin/python3`
2. Reinstall deps: `~/.bitwize-music/venv/bin/pip install -r requirements.txt --force-reinstall`
3. Run setup: `/bitwize-music:setup`

### Path Resolution Issues

```
Error: Content root not found
```

**Check config**:
```bash
/bitwize-music:configure show
```

**Verify paths exist**:
```bash
ls -la ~/music-projects  # should exist
ls -la ~/music-projects/audio
ls -la ~/music-projects/documents
```

### Venv Health Warnings

```
⚠️ Venv has 3 outdated packages
```

**Fix**:
```bash
~/.bitwize-music/venv/bin/pip install -r requirements.txt --upgrade
```

### Skills Not Loading

```
Error: Skill not found
```

**Restart Claude Code**: Plugin reloads on startup.

### Audio Mastering Fails

```
Error: matchering not installed
```

**Check venv**:
```bash
~/.bitwize-music/venv/bin/python3 -c "import matchering"
```

**Reinstall**:
```bash
~/.bitwize-music/venv/bin/pip install -r requirements.txt --force-reinstall
```

---

## Part 11: Security & Data

### Where Data Lives

| Data | Location | Backed Up | Version Controlled |
|------|----------|-----------|-------------------|
| Albums/lyrics | `{content_root}/` | You | ✅ Yes |
| Audio files | `{audio_root}/` | You | ❌ No (large files) |
| Research PDFs | `{documents_root}/` | You | ❌ No (large files) |
| Configuration | `~/.bitwize-music/config.yaml` | You | Optionally |
| State cache | `~/.bitwize-music/cache/state.json` | Auto-rebuilt | ❌ No |
| Dependencies | `~/.bitwize-music/venv/` | You | ❌ No |

### Secrets & Credentials

**Never commit secrets to the repository:**
- API keys (Suno, Anthropic, etc.)
- Database credentials
- Cloud storage keys
- Authentication tokens

**Store externally:**
- User config: `~/.bitwize-music/config.yaml`
- Cloud credentials: `~/.bitwize-music/config.yaml`
- Suno API key: Not stored (user provides on Suno.com)

### Git Safety

The plugin includes `.gitignore` rules to prevent accidentally committing:
- Audio files (`*.wav`, `*.mp3`)
- PDFs (`*.pdf`)
- Large binaries
- Configuration files

---

## Part 12: Updating & Migrations

### Checking for Updates

**Marketplace installs**:
```bash
/plugin marketplace search bitwize-music
# Shows available version
```

**Clone installs**:
```bash
cd claude-ai-music-skills
git fetch
git log --oneline origin/main -5  # See latest releases
```

### Updating

**Marketplace**:
```bash
/plugin install bitwize-music@claude-ai-music-skills --force
```

**Clone**:
```bash
git pull origin main
```

### Migrations

When updating to a new version, the plugin checks `migrations/` directory for upgrade actions:

- **Auto migrations**: Run silently (e.g., mkdir, cp)
- **Action migrations**: Ask for confirmation (e.g., file moves)
- **Info migrations**: Just announce (no action needed)
- **Manual migrations**: Instructions for you to follow

Migration files are named by version: `0.40.0.md`, `0.43.0.md`, etc.

First-time users skip migrations (no need for historical updates).

---

## Part 13: Performance & Resources

### Disk Space Estimates

| Component | Size | Notes |
|-----------|------|-------|
| Plugin code | ~50 MB | Installed once |
| Python venv | ~200 MB | Installed once |
| Per album content | 1-5 MB | Markdown files |
| Per mastered album | 500 MB - 1 GB | WAV files (lossless) |
| Research PDFs | Variable | Depends on sources |

**Total for 1 album**: ~550 MB - 1 GB

**Total for 10 albums**: ~5-10 GB

### Memory Usage

- **Claude Code**: ~500 MB - 1 GB
- **MCP server**: ~50-100 MB
- **Mastering scripts**: ~100-200 MB (peak during processing)
- **Total**: ~600 MB - 1.3 GB

### Network

- **Download dependencies**: ~100-200 MB (first time only)
- **Web research**: Variable (researcher skill)
- **Cloud uploads**: As needed (promo videos, assets)

---

## Quick Reference: Commands by Use Case

### "I just installed"
```bash
/bitwize-music:setup              # Install dependencies
/bitwize-music:configure           # Create config
/bitwize-music:tutorial help       # Learn basics
```

### "I'm stuck on setup"
```bash
/bitwize-music:setup              # Rerun setup (safe to run multiple times)
/bitwize-music:configure validate  # Check config is correct
```

### "I want to change my config"
```bash
/bitwize-music:configure show     # View current settings
/bitwize-music:configure edit     # Edit specific values
/bitwize-music:configure validate # Verify changes
```

### "Where are my files?"
```bash
/bitwize-music:configure show     # Shows all paths
# Then: ls ~/your-path            # Navigate to folder
```

### "I have old albums from before setup"
```bash
/bitwize-music:tutorial resume    # Finds all existing albums
```

### "I want to use overrides"
```bash
mkdir -p ~/music-projects/overrides
# Copy override files from config/overrides.example/
# Skills will auto-detect them
```

### "I need to debug something"
```bash
# Enable logging in config
# Then run skill
# Check logs: tail ~/.bitwize-music/logs/debug.log
```

---

## Additional Resources

- **Plugin Installation**: `/plugin help`
- **Configuration Details**: `config/README.md`
- **Override System**: `config/README.md` → "Overrides System"
- **Hooks & Validation**: `hooks/README.md`
- **Migration Process**: `migrations/README.md`
- **Testing Checklist**: `TESTING.md`
- **Security Policy**: `.github/SECURITY.md`
- **Skill Index**: `/reference/SKILL_INDEX.md`
- **Workflow Rules**: `CLAUDE.md`

---

## Summary

1. **Install** the plugin via marketplace or clone
2. **Run setup** to detect Python and install dependencies
3. **Create config** with artist name and paths
4. **Create overrides** directory (optional but recommended)
5. **Start working** — build albums with `/bitwize-music:new-album` or resume existing with `/bitwize-music:resume`

Everything else is automatic. The MCP server manages state, hooks validate quality, skills provide domain expertise, and the workflow enforces best practices at every stage.

Happy music making!
