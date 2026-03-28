# Templates and Content Structure Analysis

**Date:** 2026-03-28
**Scope:** Templates, configuration system, hooks, and plugin metadata

---

## Executive Summary

The bitwize-music plugin uses a **comprehensive template system** for managing albums, tracks, and promotional content. The configuration is **centralized at `~/.bitwize-music/config.yaml`** (outside the plugin to survive updates), with an **override system** for user customization. Git hooks enforce quality and security, while plugin metadata is strictly versioned.

**Key findings:**
- 13 templates covering albums, tracks, artists, genres, ideas, research, and 6 promo platforms
- All templates use Markdown with YAML frontmatter (tracks) or inline YAML (albums)
- Path structure strictly mirrors: `{root}/artists/[artist]/albums/[genre]/[album]/`
- 15+ override files for skill customization
- Pre-commit hook with 11 quality checks
- Semantic versioning with automatic migration support

---

## Part 1: Templates Overview

### 1.1 Core Templates (5 files)

#### **album.md**
**Purpose:** Album concept, structure, and planning
**Location:** `templates/album.md`
**Size:** ~285 lines

**Key Sections:**
- **Frontmatter:** title, release_date, genres, tags, explicit, streaming links, sheet_music
- **Album Details:** Artist, Genre, Tracks count, Status (Concept → Research Complete → Sources Verified → In Progress → Complete → Released)
- **Concept:** Detailed narrative description
- **Structure:** How tracks relate narratively
- **Themes:** Album themes (array)
- **Motifs & Threads** (concept albums):
  - Lyrical motifs (phrase → description → first appears → recurrences)
  - Character threads (name → arc → tracks list)
  - Thematic progression per track
- **OST Sections** (video game, film, TV, anime albums):
  - World/Setting table (media type, title, genre, setting, era)
  - Locations & scenes
  - Leitmotif plan (musical themes)
- **Sonic Palette:** Beats, samples, vocals, mood descriptions
- **Tracklist:** Table with # | Title | POV | Concept | Status
- **Key Characters** (narrative albums): Character groups with roles
- **Production Notes:**
  - Suno Persona (name + link)
  - Suno Settings (target duration, per-track overrides, vocal consistency, production continuity)
  - Style Prompt Base (base style to modify per track)
- **Source Material** (real events): Linked sources list
- **Documentary Standards** (true story albums):
  - Album classification (True Crime/Documentary / Dramatized / Inspired By / Fictional)
  - Narrative approach (primary voice, perspective, quote handling, artistic license)
  - Real people depicted table
  - Legal safeguards checklist (no defamation, no fabricated statements, fair use, public figures, no private facts, narrator voice)
  - Source verification status
  - Legal notes and disclaimer text
- **Album Art:** Platform selection, image prompt, negative prompt, file naming convention
- **SoundCloud:** Description, genre, tags
- **Distributor Genres:** Primary/secondary genres, electronic subgenres
- **Release Info:** Released date, track listing with duration and links

**Variables Used:**
- `[Album Title]` - Album name
- `[Artist Name]` - Artist name with optional README link
- `[Genre]` - Genre folder path
- `[Number]` - Track count
- `{content_root}` - Used in comments for album art paths
- `{audio_root}` - Used in comments for album art paths
- `{documents_root}` - Used in comments for PDF storage

**Service Tags:** `<!-- SERVICE: suno -->` and `<!-- /SERVICE: suno -->` wrap Suno-specific sections

---

#### **track.md**
**Purpose:** Individual track details, lyrics, Suno inputs, and source verification
**Location:** `templates/track.md`
**Size:** ~266 lines

**Frontmatter:**
```yaml
title: "[Track Title]"
track_number: 0
explicit: false
suno_url: ""
sheet_music:
  pdf: ""
  musicxml: ""
  midi: ""
```

**Key Sections:**
- **Track Details:** # | Title | Album link | Status | Suno Link | Stems | Explicit | POV | Role | Fade Out | Target Duration | Sources Verified

**Status Values:**
- `Not Started` → `Sources Pending` → `Sources Verified` → `In Progress` → `Generated` → `Final`

- **Source Sections** (source-based tracks):
  - Source: Name and link
  - Original Quote: Verbatim text (capture everything, no trimming)

- **Lyrical Approach** (documentary/true story):
  - Voice & Perspective (narrative voice type, are they speaking AS real person?)
  - Factual Claims Checklist (names, dates, quotes, actions, legal outcomes)
  - Quotes & Attribution (lyric line | type | how framed | source)
  - Artistic Liberties Taken (element | what changed | justification)
  - Legal Review checklist (no impersonation, documented claims, fair comment, no fabricated quotes, public interest)
  - Legal Notes

- **Concept:** Track narrative and purpose

- **Cross-References** (concept albums):
  - References TO this track
  - References FROM this track (callback, motif, character, contrast, resolution types)

- **Mood & Imagery:** Visuals and sensory details

- **Musical Direction:** Tempo, Feel, Instrumentation

- **Suno Inputs:**
  - Style Box: genre, tempo, mood, vocal description, instruments, production notes
  - Exclude Styles: Negative prompts
  - Lyrics Box: Structured with [Verse], [Chorus], [Bridge], etc. tags
  - WARNING: Suno sings everything literally including parentheticals—use metatags like [Whispered]

- **Streaming Lyrics:** Plain text (no section tags, repeats written out, plain text)

- **Production Notes:** Technical considerations, vocal delivery, sample ideas, V5 optimization

- **Pronunciation Notes:**
  - Mandatory checklist table: Word/Phrase | Pronunciation | Reason
  - Phonetic Review Checklist:
    - Proper nouns scanned
    - Foreign names
    - Homographs (live, read, lead, wind, tear)
    - Acronyms (F-B-I, G-P-S)
    - Numbers (year formats)
    - Tech terms (Linux → Lin-ucks)
  - Proper nouns table: Word | Current | Phonetic | Fixed?

- **Generation Log:** Keepers marked with ✓
  - Table: # | Date | Model | Result | Notes | Rating

- **Waveform Art** (optional):
  - ChatGPT Prompt for 2480x800px background
  - Waveform Art Link table

**Variables Used:**
- `[Track Title]` - Track name
- `XX` - Track number
- `[Album Name]` - Album name

**Service Tags:** `<!-- SERVICE: suno -->` wraps Suno-specific sections

---

#### **artist.md**
**Purpose:** Artist profile and discography
**Location:** `templates/artist.md`
**Size:** ~41 lines

**Key Sections:**
- **Artist Profile:** Name, Genre (with link), Persona, Voice, Influences
- **Background:** Backstory and character description
- **Sonic Identity:** Vocal Style, Production, Tone
- **Thematic Territory:** Array of themes
- **Discography:** Table with Album | Year | Status | Tracks
- **Visual Aesthetic:** Array of visual elements

**Variables Used:**
- `[Artist Name]` - Artist name
- `[Genre]` - Genre folder link

---

#### **genre.md**
**Purpose:** Genre overview and reference resources
**Location:** `templates/genre.md`
**Size:** ~42 lines

**Key Sections:**
- **Genre Overview:** Description, origins, characteristics, cultural context
- **Characteristics:** Lyrical focus, production, tone, community
- **Subgenres & Styles:** Table with Style | Description | Reference Artists
- **Artists:** Table with Artist | Style | Albums | Status
- **Suno Prompt Keywords:** Useful terms for generation in Suno
- **Reference Tracks:** Artist - "Track" list for style reference

**Variables Used:**
- `[Genre Name]` - Genre name
- `[artist-name]` - Artist folder path

---

#### **ideas.md**
**Purpose:** Album concept backlog before full project creation
**Location:** `templates/ideas.md`
**Size:** ~37 lines

**Idea Template:**
```markdown
### [Idea Title]

**Genre**: [hip-hop / electronic / country / folk / rock]
**Type**: [Documentary / Narrative / Thematic / Character Study / Collection / Original Soundtrack (OST)]
**Tracks**: [Estimated count]

**Concept**: [1-2 sentence pitch]
**Why this?**: [What makes it compelling?]
**Key elements**: [Element array]
**Research needed**: [Yes/No - if yes, what?]
**Status**: Pending | In Progress | Complete
```

**Status Values:**
- `Pending` - Idea captured, not started
- `In Progress` - Album directory created, actively working
- `Complete` - Album released or archived

---

### 1.2 Research Templates (2 files)

#### **research.md**
**Purpose:** Detailed research documentation with source verification
**Location:** `templates/research.md`
**Size:** ~96 lines

**Key Sections:**
- **Purpose Statement:** Legal defensibility for factual claims
- **Table of Contents**
- **Primary Sources:**
  - Official Documents (table: Source | URL | Date)
  - Investigative Journalism (table: Source | Publication | URL)
- **Timeline of Events:** Table with Date | Event | Source
- **Key People:** Per-person attributes with sources
- **Key Events:** Per-event details with sources
- **Track-by-Track Claim Verification:** Per track, table with Lyric/Claim | Verified Fact | Source
- **Areas of Creative License:** Elements that aren't verified (Element | Type | Notes)
- **Legal Notes:** Public figures, truth as defense, opinion vs fact

**Purpose:** Comprehensive factual backing for documentary albums

---

#### **sources.md**
**Purpose:** Source citations and album descriptions for distribution
**Location:** `templates/sources.md`
**Size:** ~137 lines

**Key Sections:**
- **Source Summary:** Table with Category | Count | Examples
- **Downloaded Documents:** PDFs stored externally (not in git)
  - Location: `{documents_root}/artists/[artist]/albums/[genre]/[album]/`
  - Manifest: `manifest.json`
- **Quick Reference:**
  - One-Liner: Single sentence with factual hook
  - Tagline: Memorable hook
- **Album Descriptions:** Four variants
  - SoundCloud Description (full, copy-paste ready)
  - Short (no sources, for character-limited platforms)
  - Medium (social media/blog, bulleted facts)
  - Full (liner notes, complete narrative)
- **Source Links by Category:** Tables for each source category
- **Track-by-Track Source Breakdown:** Claims and sources per track
- **Key Facts Quick Reference:** Fact | Value | Source table
- **Verified Quotes:** Table with Speaker | Quote | Date | Source
- **Social Media Copy:** References to `promo/` directory files
- **Legal Notes:** Public figures, source types, creative license

**Purpose:** Public-facing source documentation for promotion and legal defensibility

---

### 1.3 Promotional Templates (6 files)

All promo templates:
- Reference `campaign.md` strategy file
- Use `[Album Name]` and `[Track Title]` placeholders
- Include hashtag guidance
- Support both album release and per-track promotional copy

#### **campaign.md**
**Purpose:** Centralized promotional campaign planning
**Location:** `templates/promo/campaign.md`
**Size:** ~78 lines

**Key Sections:**
- **Campaign Overview:** Album name, release date, primary platform, campaign duration, language
- **Key Messages:** 1-3 main messaging angles
- **Target Audience:** Audience segments
- **Schedule:** Three phases
  - Pre-Release (teaser, album art reveal, etc.)
  - Release Week (release announcement, track promos)
  - Post-Release (behind-the-scenes, milestones)
- **Hashtags:** Primary, secondary
- **Platform Copy:** Table linking to platform-specific files (status tracking)
- **Notes:** Campaign observations and adjustments

---

#### **twitter.md**
**Purpose:** Twitter/X posting strategy
**Location:** `templates/promo/twitter.md`
**Size:** ~77 lines

**Key Sections:**
- **Release Thread:** Multi-post structure (numbered 1/, 2/, 3/, 4/)
  - Minimal hook, key fact, track highlight, stream link
- **Track Posts:** Per-track copy (1-2 sentences + link)
- **Engagement Posts:**
  - Behind the Scenes
  - Question/Poll
- **Notes:** Best practices
  - 280 char limit
  - Include streaming link
  - Space posts 1-2 days apart
  - 1-2 hashtags per tweet, never start with hashtag

---

#### **instagram.md**
**Purpose:** Instagram caption and story copy
**Location:** `templates/promo/instagram.md`
**Size:** ~87 lines

**Key Sections:**
- **Release Announcement:** Caption (2-3 sentences) + hashtag block
- **Track Highlights:** Per-track captions + hashtags
- **Story Copy:** Short punchy text
- **Hashtag Sets:** Table for Release / Track / Story
- **Notes:** Best practices
  - Max 30 hashtags (aim 15-20)
  - Hook fits in first 125 chars
  - Use Reels for video
  - Post at peak engagement times
  - Separate hashtags with line breaks

---

#### **facebook.md**
**Purpose:** Facebook longer-form posts
**Location:** `templates/promo/facebook.md`
**Size:** ~76 lines

**Key Sections:**
- **Release Post:** 2-3 paragraphs (longer storytelling format)
- **Track Highlights:** Per-track paragraphs + links
- **Engagement Posts:**
  - Discussion Prompt
  - Milestone Post
- **Notes:** Best practices
  - 150-300 word posts (longer storytelling)
  - Direct streaming links (not "link in bio")
  - Video posts get higher reach
  - 1-2 posts per week (not daily)
  - 3-5 hashtags at end

---

#### **tiktok.md**
**Purpose:** TikTok short-form video captions
**Location:** `templates/promo/tiktok.md`
**Size:** ~70 lines

**Key Sections:**
- **Release Announcement:** Brief caption (one punchy sentence)
- **Track Captions:** Per-track short captions
- **Content Ideas:**
  - Behind the Scenes: "How I made [track] with AI"
  - Before/After: "First draft vs final version"
- **Notes:** Best practices
  - Keep captions short (150 chars ideal)
  - Video content does heavy lifting
  - Use trending sounds/formats
  - Post during peak hours
  - 3-5 hashtags per post

---

#### **youtube.md**
**Purpose:** YouTube video descriptions and Shorts
**Location:** `templates/promo/youtube.md`
**Size:** ~86 lines

**Key Sections:**
- **Full Album Video:**
  - Description: 2-3 paragraphs + full tracklist with timestamps + streaming links + credits
- **Per-Track Videos:** 1-2 paragraphs per track + stream link
- **Shorts:** Track highlight with "Full track in description!"
- **Notes:** Best practices
  - Include timestamps for full albums
  - Add streaming links to all platforms
  - 3-5 hashtags (first 3 shown above title)
  - Shorts descriptions < 100 characters
  - Hook in first 2-3 lines

---

### 1.4 Summary: Template Statistics

| Category | Template | Lines | Key Sections |
|----------|----------|-------|--------------|
| **Core** | album.md | ~285 | 15+ sections, full album concept |
| | track.md | ~266 | 13+ sections, lyrics, Suno, sources |
| | artist.md | ~41 | Profile, discography, aesthetic |
| | genre.md | ~42 | Genre overview, reference tracks |
| | ideas.md | ~37 | Idea capture with status |
| **Research** | research.md | ~96 | Detailed source verification |
| | sources.md | ~137 | Public-facing citations |
| **Promo** | campaign.md | ~78 | Campaign planning |
| | twitter.md | ~77 | Thread and engagement posts |
| | instagram.md | ~87 | Captions, stories, hashtags |
| | facebook.md | ~76 | Longer storytelling posts |
| | tiktok.md | ~70 | Short captions and content ideas |
| | youtube.md | ~86 | Full descriptions and timestamps |
| **TOTAL** | **13 files** | **~1,478** | **Comprehensive system** |

---

## Part 2: Album Directory Layout

### 2.1 Expected File Structure

The plugin enforces a **strict mirrored structure** across three roots:

```
{content_root}/artists/[artist]/albums/[genre]/[album]/
├── README.md                          # Album concept and planning
├── SOURCES.md                         # Source citations (if documentary)
├── RESEARCH.md                        # Detailed research (if documentary)
├── tracks/
│   ├── 01-track-name.md              # Track 1: lyrics, Suno inputs
│   ├── 02-track-name.md              # Track 2
│   ├── ...
│   └── NN-track-name.md              # Last track
├── promo/                             # Social media copy (optional)
│   ├── campaign.md                    # Campaign planning
│   ├── twitter.md                     # Twitter/X posts
│   ├── instagram.md                   # Instagram captions
│   ├── tiktok.md                      # TikTok captions
│   ├── facebook.md                    # Facebook posts
│   └── youtube.md                     # YouTube descriptions
└── album-art.png                      # Artwork (tracked in git)

{audio_root}/artists/[artist]/albums/[genre]/[album]/
├── album.png                          # Album artwork (for SoundCloud, promo videos)
├── 01-track-name.wav                  # Mastered audio
├── 02-track-name.wav
├── ...
├── NN-track-name.wav
└── promo_videos/                      # 15-second vertical videos (9:16)
    ├── 01-track-name.mp4
    ├── 02-track-name.mp4
    ├── ...
    └── album-sampler.mp4              # If include_sampler enabled

{documents_root}/artists/[artist]/albums/[genre]/[album]/
├── manifest.json                      # PDF metadata (auto-generated)
├── indictment.pdf                     # Primary source documents
├── sec-filing.pdf
├── plea-agreement.pdf
└── ...                                # Other PDFs
```

### 2.2 Path Resolution

All paths support `~` expansion and variable substitution:

| Variable | Resolves From | Example |
|----------|---------------|---------|
| `{content_root}` | `paths.content_root` | `~/music-projects` |
| `{audio_root}` | `paths.audio_root` | `~/music-projects/audio` |
| `{documents_root}` | `paths.documents_root` | `~/music-projects/documents` |
| `[artist]` | `artist.name` | `bitwize` |
| `[genre]` | Album genre | `electronic` |
| `[album]` | Album name | `dark-futures` |

### 2.3 Mirroring Requirement

**Critical:** Audio and document paths include `artists/[artist]/` **after the root**, not directly at the root.

**Correct:**
```
~/audio/artists/bitwize/albums/electronic/my-album/
```

**Incorrect (common mistake):**
```
~/audio/my-album/                   # Missing full artist/genre structure
```

### 2.4 Track File Naming

- **Zero-padded:** `01-`, `02-`, ..., `09-`, `10-`, `11-`, `99-`
- **Slug format:** lowercase, hyphens for spaces
- **Examples:**
  - `01-intro.md`
  - `05-the-darkest-hour.md`
  - `12-outro.md`

### 2.5 File Organization Best Practices

**Content Root (version-controlled):**
- `README.md` - Album concept (always included)
- `SOURCES.md` - For public distribution
- `RESEARCH.md` - For internal reference (documentary albums)
- `tracks/` - Lyrics and metadata
- `promo/` - Social media copy
- `album-art.png` - Artwork (if including in git)

**Audio Root (NOT version-controlled):**
- `.gitignore` prevents tracking large WAV files
- `album.png` - Single copy used by SoundCloud, promo videos
- `*.wav` - Mastered audio
- `promo_videos/` - Generated MP4 files

**Documents Root (separate from both):**
- Primary source PDFs (court docs, SEC filings, articles)
- `manifest.json` - Metadata
- Stored separately because not needed in git or with audio

---

## Part 3: Configuration System

### 3.1 Configuration File

**Location:** `~/.bitwize-music/config.yaml` (outside plugin directory)

**Why outside?**
1. Survives plugin updates
2. Always at same location (whether cloned or installed)
3. Easy access and backup
4. Can be version-controlled separately

### 3.2 Configuration Structure

#### **REQUIRED: Artist Section**

```yaml
artist:
  name: "your-artist-name"  # REQUIRED
  genres:                    # OPTIONAL (used as defaults)
    - "electronic"
    - "hip-hop"
  style: "dark industrial..."  # OPTIONAL (helps Claude understand your vibe)
```

**Important:**
- `name` is case-sensitive and used in folder paths
- Use lowercase-with-hyphens for consistency
- No spaces (use hyphens: "dj-shadow-clone" not "dj shadow clone")

#### **REQUIRED: Paths Section**

```yaml
paths:
  content_root: "~/music-projects"              # Albums, artists, research
  audio_root: "~/music-projects/audio"          # Mastered audio
  documents_root: "~/music-projects/documents"  # PDFs, primary sources
  overrides: "~/music-projects/overrides"       # OPTIONAL (default: {content_root}/overrides)
  ideas_file: "~/music-projects/IDEAS.md"       # OPTIONAL (default: {content_root}/IDEAS.md)
```

**Critical: All paths mirror the structure**
```
{content_root}/artists/[artist]/albums/[genre]/[album]/
{audio_root}/artists/[artist]/albums/[genre]/[album]/
{documents_root}/artists/[artist]/albums/[genre]/[album]/
```

#### **OPTIONAL: Platform URLs**

```yaml
urls:
  website: "https://your-artist.com"
  soundcloud: "https://soundcloud.com/your-artist"
  spotify: "https://open.spotify.com/artist/..."
  apple_music: "https://music.apple.com/us/artist/..."
  bandcamp: "https://your-artist.bandcamp.com"
  youtube_music: "https://music.youtube.com/channel/..."
  youtube: "https://youtube.com/@your-artist"
  twitter: "https://x.com/your-artist"
  instagram: "https://instagram.com/your-artist"
  tiktok: "https://tiktok.com/@your-artist"
  linktree: "https://linktr.ee/your-artist"
```

#### **OPTIONAL: Generation Service**

```yaml
generation:
  service: suno                          # OPTIONAL (default: suno)
  require_suno_link_for_final: true      # OPTIONAL (default: true)
  max_lyric_words: 800                   # OPTIONAL (default: 800)
  require_source_path_for_documentary: true  # OPTIONAL (default: true)
  additional_genres: ["synthwave", "lo-fi"]  # OPTIONAL
```

#### **OPTIONAL: Promo Videos**

```yaml
promotion:
  default_style: "pulse"                 # pulse, bars, line, mirror, mountains, colorwave, neon, dual, circular
  duration: 15                           # seconds (15, 30, 60)
  include_sampler: true                  # Generate album sampler
  sampler_clip_duration: 12              # Seconds per track in sampler
```

#### **OPTIONAL: Sheet Music**

```yaml
sheet_music:
  enabled: true                          # OPTIONAL (default: true)
  page_size: "letter"                    # letter, 9x12, 6x9
  section_headers: false                 # Include [Verse], [Chorus] labels
  footer_url: "https://your-website.com"  # OPTIONAL (auto-detected from urls)
```

#### **OPTIONAL: Cloud Storage**

```yaml
cloud:
  enabled: true
  provider: "r2"                         # r2 (Cloudflare) or s3 (AWS)
  public_read: true                      # Make uploads publicly accessible
  r2:
    account_id: "your-32-char-id"
    access_key_id: "your-key"
    secret_access_key: "your-secret"
    bucket: "promo-videos"
    public_url: ""                       # Optional custom domain
  # OR:
  # s3:
  #   region: "us-west-2"
  #   access_key_id: "..."
  #   secret_access_key: "..."
  #   bucket: "my-promo-bucket"
```

#### **OPTIONAL: Database**

```yaml
database:
  enabled: true
  host: "localhost"                      # Or remote PostgreSQL host
  port: 5432                             # OPTIONAL (default: 5432)
  name: "your-database"
  user: "your-username"
  password: "your-password"
```

#### **OPTIONAL: Logging**

```yaml
logging:
  enabled: true
  level: "debug"                         # debug, info, warning, error
  file: "~/.bitwize-music/logs/debug.log"  # OPTIONAL
  max_size_mb: 5                         # Before rotation
  backup_count: 3                        # Number of rotated files to keep
```

### 3.3 Configuration Examples

#### **Pattern 1: Minimal (Getting Started)**
```yaml
artist:
  name: "your-artist"

paths:
  content_root: "~/music-projects"
  audio_root: "~/music-projects/audio"
  documents_root: "~/music-projects/documents"
```

#### **Pattern 2: Full Featured**
```yaml
artist:
  name: "bitwize"
  genres: ["electronic", "hip-hop", "industrial"]
  style: "dark industrial electronic with aggressive vocals"

paths:
  content_root: "~/bitwize-music"
  audio_root: "~/bitwize-music/audio"
  documents_root: "~/bitwize-music/documents"
  overrides: "~/bitwize-music/overrides"

urls:
  soundcloud: "https://soundcloud.com/bitwize"
  twitter: "https://x.com/bitwize"

generation:
  service: suno

promotion:
  default_style: "neon"
  include_sampler: true

sheet_music:
  page_size: "9x12"
```

#### **Pattern 3: macOS Standard**
```yaml
artist:
  name: "your-artist"

paths:
  content_root: "~/Music/ai-music-projects"
  audio_root: "~/Music/ai-music-projects/mastered"
  documents_root: "~/Documents/ai-music-research"
```

#### **Pattern 4: WSL with Windows Paths**
```yaml
artist:
  name: "your-artist"

paths:
  content_root: "/mnt/c/Users/YourName/Music/ai-music"
  audio_root: "/mnt/c/Users/YourName/Music/ai-music/audio"
  documents_root: "/mnt/c/Users/YourName/Documents/ai-music-docs"
```

#### **Pattern 5: Separate Drives (Large Catalogs)**
```yaml
artist:
  name: "prolific-producer"

paths:
  content_root: "~/music-projects"           # SSD
  audio_root: "/media/external/music-audio"  # External drive
  documents_root: "~/music-projects/docs"
```

### 3.4 Override System

**Location:** `{paths.overrides}` directory (default: `{content_root}/overrides`)

**Purpose:** Customize skills and workflows without plugin update conflicts

#### **Available Override Files**

| File | Purpose | Format | Skill Used By |
|------|---------|--------|---------------|
| `CLAUDE.md` | Workflow instructions | Markdown | Session start |
| `pronunciation-guide.md` | Phonetic spellings | Markdown table | pronunciation-specialist |
| `explicit-words.md` | Custom explicit list | Markdown | explicit-checker |
| `lyric-writing-guide.md` | Writing preferences | Markdown | lyric-writer |
| `suno-preferences.md` | Generation settings | Markdown table | suno-engineer |
| `mastering-presets.yaml` | EQ/dynamics overrides | YAML | mastering-engineer |
| `album-planning-guide.md` | Planning preferences | Markdown | album-conceptualizer |
| `album-art-preferences.md` | Visual style | Markdown | album-art-director |
| `research-preferences.md` | Research standards | Markdown | researcher |
| `release-preferences.md` | QA and platforms | Markdown | release-director |
| `promotion-preferences.md` | Promo style | Markdown | promo-writer |
| `sheet-music-preferences.md` | Notation layout | Markdown | sheet-music-publisher |

#### **Override Behavior**

**Merging:** Most overrides **merge** with base files (custom takes precedence)
**Replacing:** Some overrides **replace** entirely (e.g., `mastering-presets.yaml`)
**Supplementing:** CLAUDE.md **supplements** base instructions

#### **Example: Custom Pronunciation Guide**

```markdown
# Custom Pronunciation Guide

## Artist-Specific Terms
| Word | Standard | Phonetic | Notes |
|------|----------|----------|-------|
| BitWize | bitwize | Bit-Wize | Artist name |
| Larocca | larocca | Luh-rock-uh | Character name |
```

Behavior: Loaded at track generation, merged with base guide, custom takes precedence.

#### **Example: Custom Mastering Presets (YAML)**

```yaml
genres:
  dark-electronic:
    cut_highmid: -3
    boost_sub: 2
    target_lufs: -12
```

Behavior: Loaded by mastering-engineer, **overrides** default presets.

#### **Setup**

```bash
mkdir -p ~/music-projects/overrides
cp config/overrides.example/pronunciation-guide.md ~/music-projects/overrides/
cp config/overrides.example/suno-preferences.md ~/music-projects/overrides/
# etc.
```

---

## Part 4: Hooks System

### 4.1 Pre-Commit Hook

**Location:** `hooks/pre-commit`
**Installed to:** `.git/hooks/pre-commit`
**Execution:** Automatic on `git commit`
**Bypass:** `git commit --no-verify` (not recommended)

### 4.2 11 Quality Checks

| # | Check | Tool | Purpose |
|---|-------|------|---------|
| 1 | Ruff Linter | ruff | Code style and quality |
| 2 | JSON/YAML Validation | python3 | Config file syntax |
| 3 | CLAUDE.md Size | wc | Keep under 40K characters |
| 4 | Version Sync | python3 | plugin.json and marketplace.json match |
| 5 | Skill Frontmatter | python3 + YAML | All skills have valid YAML frontmatter |
| 6 | CHANGELOG Format | grep | Has [Unreleased] section |
| 7 | Merge Conflict Markers | grep | No unresolved conflicts |
| 8 | Large Files | wc | No files >600KB |
| 9 | Security Scan | bandit + pip-audit | Code and dependency vulnerabilities |
| 10 | Unit Tests | pytest | All tests pass |
| 11 | Test Count Badge | pytest | README badge matches actual count |

### 4.3 Post-Tool Hooks

**Location:** `hooks/hooks.json`
**Execution:** After Write/Edit tool use
**Hooks:**
1. `validate_track.py` - Validates track files (5s timeout)
2. `check_version_sync.py` - Ensures version file sync (5s timeout)

### 4.4 Hook Installation

**Automatic (with setup):**
```bash
bash hooks/install.sh
```

**Manual:**
```bash
cp hooks/pre-commit .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

### 4.5 Development Notes

- Keep hooks fast (<30 seconds for most commits)
- Test locally before pushing
- Update README.md when adding new checks
- Hooks are **enforcing** quality gates, not advisory

---

## Part 5: Plugin Metadata

### 5.1 plugin.json

**Location:** `.claude-plugin/plugin.json`
**Purpose:** Plugin registration and configuration

```json
{
  "name": "bitwize-music",
  "description": "AI music generation workflow for Suno - album concepts, lyrics, prompts, mastering, release",
  "version": "0.81.2",
  "author": {
    "name": "bitwize-music"
  },
  "repository": "https://github.com/bitwize-music-studio/claude-ai-music-skills",
  "homepage": "https://github.com/bitwize-music-studio/claude-ai-music-skills#readme",
  "license": "CC0-1.0",
  "skills": "./skills/",
  "mcpServers": "./.mcp.json",
  "keywords": [
    "music", "suno", "lyrics", "album", "mastering",
    "ai-music", "claude-code", "music-production",
    "songwriting", "audio-mastering", "music-generation",
    "suno-ai", "lyric-writing", "album-release",
    "music-workflow", "promo-videos", "social-media",
    "video-generation"
  ]
}
```

**Key Fields:**
- `name` - Plugin identifier (lowercase, kebab-case)
- `version` - [Semantic versioning](https://semver.org/) (MAJOR.MINOR.PATCH)
- `skills` - Path to skills directory (must be included)
- `mcpServers` - Path to MCP server config
- `license` - CC0-1.0 (public domain)

### 5.2 marketplace.json

**Location:** `.claude-plugin/marketplace.json`
**Purpose:** Plugin marketplace listing

```json
{
  "name": "bitwize-music",
  "owner": {
    "name": "bitwize-music-studio"
  },
  "plugins": [
    {
      "name": "bitwize-music",
      "description": "AI music generation workflow for Suno - album concepts, lyrics, prompts, mastering, release",
      "version": "0.81.2",
      "source": "./"
    }
  ]
}
```

**Critical:** Version in marketplace.json **MUST match** plugin.json (enforced by pre-commit hook #4)

### 5.3 Versioning Strategy

**Semantic Versioning:**
- `MAJOR.MINOR.PATCH` (e.g., `0.81.2`)

**Conventional Commits:**
| Prefix | Version Bump |
|--------|--------------|
| `feat:` | MINOR |
| `fix:` | PATCH |
| `feat!:` | MAJOR |
| `docs:`, `chore:` | None |

**Co-Authoring:**
```
Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
```

**Release Process:**
1. Update `CHANGELOG.md`: `[Unreleased]` → `[0.x.0 - DATE]`
2. Update version in both `.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json`
3. Update `README.md` "What's New" table (if notable)
4. Commit: `chore: release 0.x.0`
5. Merge `develop` → `main`

### 5.4 Migration Support

**Location:** `{plugin_root}/migrations/*.md`

When plugin version increments:
1. Check current version in `state.json` (`plugin_version` field)
2. If `null` → first run, set to current version
3. If stored < current → read applicable migration files
4. Process migration actions before session starts

---

## Part 6: New User Setup Checklist

### Step 1: Create Config Directory
```bash
mkdir -p ~/.bitwize-music
```

### Step 2: Copy Config Template
```bash
cp config/config.example.yaml ~/.bitwize-music/config.yaml
```

### Step 3: Edit Configuration
```bash
nano ~/.bitwize-music/config.yaml
```

**Required fields:**
- `artist.name` - Your artist name (e.g., "my-artist")
- `paths.content_root` - Where albums live (e.g., "~/music-projects")
- `paths.audio_root` - Where audio goes (e.g., "~/music-projects/audio")
- `paths.documents_root` - Where PDFs go (e.g., "~/music-projects/documents")

### Step 4: Create Content Directories
```bash
mkdir -p ~/music-projects/audio ~/music-projects/documents ~/music-projects/overrides
```

### Step 5: (Optional) Setup Overrides
```bash
cp config/overrides.example/pronunciation-guide.md ~/music-projects/overrides/
cp config/overrides.example/suno-preferences.md ~/music-projects/overrides/
# etc. for any other overrides you need
```

### Step 6: (Optional) Install Git Hooks
```bash
bash hooks/install.sh
```

### Step 7: Initialize Session
Run `/bitwize-music:session-start` in Claude Code to load config and verify setup.

### Step 8: Create First Album
Run `/bitwize-music:new-album` to create your first album structure.

### Verification Checklist

- [ ] Config file exists at `~/.bitwize-music/config.yaml`
- [ ] Required fields filled in (artist.name, 3 paths)
- [ ] Content directories created
- [ ] Can read `ls ~/music-projects/`
- [ ] Can read `ls ~/music-projects/audio/`
- [ ] Session start runs without config errors
- [ ] First album created successfully

---

## Part 7: Service Tags System

Templates use conditional sections for different music generation services.

### 7.1 Service Tag Format

```markdown
<!-- SERVICE: suno -->
[Suno-specific section]
<!-- /SERVICE: suno -->
```

### 7.2 Currently Supported

- `suno` - Suno V5 (default, recommended)

**Future support planned:**
- `udio` - Udio
- `soundraw` - SoundRaw

### 7.3 Service-Specific Sections

**album.md:**
- Suno Persona
- Suno Settings (target duration, vocal consistency, production notes)
- Style Prompt Base

**track.md:**
- Style Box (genre, tempo, mood, vocal, instruments, production)
- Exclude Styles (negative prompts)
- Lyrics Box (structured with section tags)
- Phonetic Review Checklist (proper nouns, foreign names, homographs, etc.)
- V5 optimization tips

---

## Part 8: Key Design Principles

### 8.1 Separation of Concerns

- **Content** (`{content_root}`) - Markdown files, version-controlled
- **Audio** (`{audio_root}`) - Large binary WAV files, NOT version-controlled
- **Documents** (`{documents_root}`) - PDF sources, separate from both
- **Config** (`~/.bitwize-music/`) - User settings, survives updates

### 8.2 Mirrored Paths

All paths use identical structure: `artists/[artist]/albums/[genre]/[album]/`

This enables:
- Consistent file lookups
- Easy parallel operations across all roots
- Clear dependency tracking
- Simple disaster recovery

### 8.3 Template as Contract

Templates define:
- Required sections (album concept, tracklist)
- Optional sections (OST, documentary, character threads)
- Field formats (tables, frontmatter, code blocks)
- Placeholder patterns (`[Name]`, `{root}`)

Templates are **not** generated code—users edit them directly. Sections can be deleted if not applicable.

### 8.4 Override Precedence

1. Plugin defaults
2. Override files (if exist at `{overrides}/`)
3. User-edited content

Override files are **additive** (except mastering presets which replace) and skills merge them intelligently.

### 8.5 Git Hooks as Quality Gates

Pre-commit hooks enforce:
- Code quality (linting, tests, security)
- Configuration consistency (version sync)
- Documentation standards (CLAUDE.md size, CHANGELOG format)
- Release readiness (no conflicts, no large files)

Hooks can be bypassed but should not be—they catch issues early.

---

## Summary Table

| Component | Location | Purpose | Required |
|-----------|----------|---------|----------|
| Templates | `templates/` | Album, track, promo structure | Yes |
| Config | `~/.bitwize-music/config.yaml` | User settings | Yes |
| Overrides | `{content_root}/overrides/` | Skill customization | No |
| Hooks | `hooks/` | Quality enforcement | No (but recommended) |
| Plugin Metadata | `.claude-plugin/` | Version and registration | Yes |

**Total Files:** 13 templates + 12 override examples + 2 plugin metadata files + 1 config template

**Total Lines:** ~1,478 (templates) + ~840 (config) + ~500 (hooks) = ~2,818 lines of configuration and structure

---

## Conclusion

The bitwize-music plugin uses a **comprehensive, opinionated system** for structuring music production:

1. **Templates** define the shape of albums, tracks, and promo content
2. **Configuration** is user-controlled, externalized, and override-friendly
3. **Hooks** enforce quality and security automatically
4. **Mirrored paths** create consistency across content, audio, and documents
5. **Service tags** allow future expansion to other music generation services

This system is designed for **musicians and songwriters**, not just developers. Every template includes guidance on what to fill in, why it matters, and how it connects to the workflow.

**New users should:**
1. Copy config template and edit 3 fields
2. Create content/audio/documents directories
3. (Optional) Copy any override files they need
4. Run `/bitwize-music:session-start` to verify setup
5. Create first album with `/bitwize-music:new-album`

