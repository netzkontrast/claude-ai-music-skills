# Reference Documentation & Suno Deep Dive
## Complete Catalog for AI Music Skills Plugin

**Report Date:** March 28, 2026
**Scope:** All reference documentation, Suno guides, workflow references, and developer documentation

---

## Overview

The claude-ai-music-skills plugin contains a comprehensive documentation ecosystem with three primary categories:

1. **Reference Documentation** - Technical and procedural guides (15+ files across 10+ subdirectories)
2. **Developer Documentation** - Contributing, testing, and plugin development
3. **Project Documentation** - Configuration, skills overview, troubleshooting

This report catalogs every reference file with summaries and key takeaways for setting up your own album workflow.

---

## Reference Documentation Map

### Top-Level Reference Files

| File | Summary | Key Value |
|------|---------|-----------|
| **[SKILL_INDEX.md](../reference/SKILL_INDEX.md)** | Complete skill reference with decision trees, prerequisites, common sequences | Master index for skill routing; shows which skill for any task |
| **[state-schema.md](../reference/state-schema.md)** | Cache structure v1.2.0 with album/track/skill/session data models | Understanding how project state is stored and queried |
| **[terminology.md](../reference/terminology.md)** | 150+ defined terms: core concepts, album workflow, Suno terms, audio terms | Standardized vocabulary for the entire plugin ecosystem |
| **[distribution.md](../reference/distribution.md)** | Streaming lyrics format, explicit content guidelines with word lists | Pre-release checklist for metadata compliance |
| **[model-strategy.md](../reference/model-strategy.md)** | Rationale for Opus/Sonnet/Haiku tier assignment to 51 skills | Understanding why each skill uses its assigned model |

---

## Suno Deep Dive

### Suno Reference Overview

The Suno reference directory is the most critical collection for actually generating music. These guides bridge the gap between lyric writing and Suno API parameters.

**Location:** `/reference/suno/` (11 files + changelog + version history)

### Core Suno Guides

#### [V5 Best Practices](../reference/suno/v5-best-practices.md)
**What it covers:**
- Quick-start formula: `[genre], [subgenre], [instruments], [mood], [tempo], [vocal description]`
- V5 key improvements: intelligent composition, studio-grade audio (44.1kHz), 12-stem extraction, 10x faster generation
- Critical rule: **Never reuse old V4/V4.5 prompts on V5** — CTO recommendation
- Prompt construction: 4-7 descriptors (avoid "prompt fatigue" with 8+)
- Four-part anatomy: Genre/Era + Tempo/Key + Instrumentation + Production/Mix notes
- Genre-specific tips for hip-hop, punk, electronic, folk, K-pop with detailed K-pop switch-up patterns

**Key Takeaway:** V5 is simpler than V4 — it interprets context better, so shorter, clearer prompts work better. Write new prompts specifically for V5.

---

#### [Pronunciation Guide](../reference/suno/pronunciation-guide.md)
**What it covers:**
- 20+ high-risk homographs (live/read/lead/wind/close/tear/bass/wound/minute/resume/object/project/record/present/content/desert/refuse)
- Deep dive on "live" pronunciation (LYVE vs LIV) with decision guide and fix options
- 40+ medium-risk homographs with context notes
- Tech terms and brand names pronunciation (LiveJournal, YouTube, GitHub, etc.)
- Acronyms and initialisms with Suno-specific spelling (e.g., "Lin-ucks" for Linux)

**Key Takeaway:** Suno reads literally—never trust context for homographs. Use phonetic spelling: "lyve your life," "Lin-ucks lab," "Rah-mohs." IPA is not natively supported.

---

#### [Structure Tags Reference](../reference/suno/structure-tags.md)
**What it covers:**
- Basic structure tags: `[Intro]`, `[Verse]`, `[Verse 1-2]`, `[Chorus]`, `[Bridge]`, `[Outro]`, `[Fade Out]`, `[Break]`, `[Interlude]`
- **Critical note:** `[Intro]` is unreliable; use `[Short Instrumental Intro]` or `[Intro - Spoken]` instead
- Custom mood/style tags: `[Shout]`, `[Whimsical]`, `[Melancholy]`, `[Spoken]`, `[Whispered]`, `[Energetic]`
- Example song structures for pop/rock, hip-hop, punk
- V5 bar count targeting: `[INTRO 4] [VERSE 1 8] [PRE 4] [CHORUS 8]...` (approximate guidance, not strict)
- Reliability notes: `[Verse]`, `[Chorus]`, `[End]`, `[Fade Out]` most reliable; `[Intro]` least reliable

**Key Takeaway:** Use numbered verses, avoid `[Intro]`, end explicitly with `[End]` or `[Fade Out]`. Combine tags with descriptions: `[Soft Verse]`, `[Building Chorus]`.

---

#### [Voice Tags Reference](../reference/suno/voice-tags.md)
**What it covers:**
- V5 dedicated Voice Gender selector in Advanced Options (most reliable method for gender control)
- 18 vocal style tags: staccato, legato, vibrato-heavy, monotone, melismatic, syncopated, operatic, chanting, spoken-word, growling, belting, yodeling, scatting, etc.
- 15 vocal texture tags: whispered, gravelly, velvety, dreamy, resonant, nasal, brassy, metallic, saturated, smoky, breathy, etc.
- Regional vocal styles: British rock, Southern gospel, Nashville country, New York hip-hop, Jamaican dancehall, Irish folk
- **Warning:** Many voice tags are hit-or-miss; reliable strategy is to upload Splice vocal sample, then use Extend/Cover with voice tags

**Key Takeaway:** Use Advanced Options gender selector for baseline. For consistency across an album, use Personas (Pro/Premier feature) instead of relying on style prompt tags.

---

#### [Tips & Tricks](../reference/suno/tips-and-tricks.md)
**What it covers:**
- Lyrics not audible: simplify prompts, use `[Short Instrumental Intro]`, describe vocals explicitly ("clear and prominent vocals")
- Extending songs: each extension generates ~1 min, creates 2 clips; use Extend From Timestamp for mid-song fixes
- Lyrics going wrong: go back to earlier clip, use Extend From Time, generate multiple versions
- Replace Section feature (Pro/Premier): edit lyrics or add instrumental breaks in 10-30 second segments
- Layering styles: combine multiple prompts for complex tracks
- Save style prompts: bookmark successful prompts, access via library book icon for album consistency
- **Download limits (Nov 2025 update):** Free=no downloads; Pro=monthly limit; Premier=unlimited in Suno Studio

**Key Takeaway:** Extend from earlier timestamps instead of regenerating entire tracks. Save successful style prompts for album consistency. Premier is needed for unlimited downloads.

---

#### [Genre List](../reference/suno/genre-list.md)
**What it covers:**
- 500+ music styles organized by category:
  - Electronic & Dance (60+ genres)
  - Hip Hop & Rap (15+ genres)
  - Jazz & Blues (20+ genres)
  - Rock & Metal (40+ genres)
  - Pop & Contemporary (30+ genres)
  - Folk & Country (15+ genres)
  - World & Cultural (25+ genres)
  - Classical & Orchestral (15+ genres)
  - Soundtrack & Theme (8+ genres)
  - Experimental & Avant-garde (12+ genres)

**Key Takeaway:** Use specific subgenres in style prompts (e.g., "boom bap," "trap," "lo-fi" instead of generic "hip-hop"). Combine genres for fusions: "disco funk," "Arabian rock."

---

#### [Instrumental Tags Reference](../reference/suno/instrumental-tags.md)
**What it covers:**
- Specific instruments and their Suno names
- When to set `Instrumental: On` vs. using lyrics with `[Instrumental]` sections
- Instrument control in style prompts vs. section tags

**Key Takeaway:** For OST albums, use `Instrumental: On` in API parameters for instrumental-only tracks. Use `[Instrumental Break]` sections within vocal tracks.

---

#### Supporting Suno Files

| File | Purpose |
|------|---------|
| **[README.md](../reference/suno/README.md)** | Navigation guide for all Suno docs; quick reference table for which guide to use for each task |
| **[CHANGELOG.md](../reference/suno/CHANGELOG.md)** | Chronological log of Suno updates and doc changes |
| **[version-history/v5-changes.md](../reference/suno/version-history/v5-changes.md)** | Migration guide from V4 to V5 |
| **[workspace-management.md](../reference/suno/workspace-management.md)** | Manual organization strategies for Suno library |
| **[artist-blocklist.md](../reference/suno/artist-blocklist.md)** | Artists that Suno won't generate in copyright scenarios |

---

## Workflow Guides Summary

### Album Planning Workflow

**[Album Planning: The 7 Phases](../reference/workflows/album-planning-phases.md)**

Seven mandatory planning phases before any writing begins:

1. **Foundation** - Artist, genre, album type, track count, true-story determination
2. **Concept Deep Dive** - Central story/theme, key characters, narrative arc, emotional core
3. **Sonic Direction** - Inspiration artists, production style, vocal approach, instrumentation, mood, target duration
4. **Structure Planning** - Tracklist with track-by-track concepts, narrative flow, pivotal tracks, pacing
5. **Album Art** - Visual concept, color palette, symbolic elements
6. **Practical Details** - Album/track titles finalized, research needs, explicit content expectation, distributor genres
7. **Confirmation** - Present complete plan, get explicit go-ahead from user

**Key Rule:** No track writing until all 7 phases complete and user confirms "Ready to start writing."

---

### Status Tracking

**[Status Tracking](../reference/workflows/status-tracking.md)**

**Track statuses (in order):**
- `Not Started` → `Sources Pending` → `Sources Verified` → `In Progress` → `Generated` → `Final`

**Album statuses (in order):**
- `Concept` → `Research Complete` → `Sources Verified` → `In Progress` → `Complete` → `Released`

**Generation Log:** Every track includes a table logging attempts: Date, Model (V5), Result (URL), Notes, Rating (✓ for keepers).

**Album-wide rule:** Album advances to next status only when ALL tracks reach corresponding level. A single unverified track blocks album from advancing past "Research Complete."

---

### Additional Workflow Guides

| File | Purpose |
|------|---------|
| **[Source Verification Handoff](../reference/workflows/source-verification-handoff.md)** | Process for transitioning from automated research to human verification gate |
| **[Importing Audio](../reference/workflows/importing-audio.md)** | WAV import procedures and path setup |
| **[Error Recovery](../reference/workflows/error-recovery.md)** | Troubleshooting common workflow blockers |
| **[Checkpoint Scripts](../reference/workflows/checkpoint-scripts.md)** | Validation checkpoints before major phases |
| **[Release Procedures](../reference/workflows/release-procedures.md)** | Distribution coordination workflow |

---

## Key Production References

### Audio Mastering

**[Mastering Workflow](../reference/mastering/mastering-workflow.md)**

Complete automated mastering pipeline for streaming release:

**Workflow Steps:**
1. **Analysis Phase** - LUFS analysis, identify problem tracks (tinny, quiet, loud, high dynamic range)
2. **Mastering Phase** - Apply corrective EQ, normalize to -14 LUFS (streaming standard), limit peaks to -1.0 dBTP
3. **Reference-based Mastering** - Match tonal balance to professionally mastered reference track
4. **Quality Checklist** - Verify album consistency (< 1 dB LUFS variation)

**Key Metrics:**
- Target LUFS: -14 to -16 (varies < 2 dB across album)
- Peak dB: -1 to -3 (not above -0.5 to avoid clipping)
- High-Mid Energy: 8-15% (> 20% = tinny)

**Tools Required:**
- Python: matchering, pyloudnorm, scipy, numpy, soundfile (one-time setup in `~/.bitwize-music/venv/`)
- Scripts: `/tools/mastering/analyze_tracks.py`, `master_tracks.py`

**Genre Presets:** 60+ built-in presets for pop, hip-hop, rock, jazz, folk, etc. with automatic EQ/dynamics settings.

---

### Audio Engineering References

| File | Purpose |
|------|---------|
| **[Mastering Checklist](../reference/mastering/mastering-checklist.md)** | Pre-release QA checklist |
| **[Genre-Specific Presets](../reference/mastering/genre-specific-presets.md)** | EQ and dynamics settings by genre |
| **[Loudness Measurement](../reference/mastering/loudness-measurement.md)** | LUFS and true peak measurement techniques |

---

### Promotion & Release

**[Promo Video Workflow](../reference/promotion/promo-workflow.md)**

Generate 15-second vertical promo videos (9:16) for social media:

**Prerequisites:**
- Mastered audio (WAV, MP3, FLAC, M4A)
- Album artwork (3000x3000px PNG/JPG)
- ffmpeg with filters (showwaves, showfreqs, drawtext, gblur)

**Visualization Styles:**
- Electronic/Hip-Hop: pulse
- Pop/Rock: bars
- Acoustic/Folk: line
- Ambient/Chill: mirror
- Synthwave/80s: neon

---

**[Social Media Best Practices](../reference/promotion/social-media-best-practices.md)**

Platform-specific optimization for TikTok, Instagram, Twitter, YouTube, Facebook.

---

**[Distributor Guide](../reference/release/distributor-guide.md)**

**Major distributors comparison:**

| Distributor | Pricing Model | Best For |
|-------------|---------------|----------|
| DistroKid | $22.99/year | Frequent releasers, singles |
| TuneCore | $9.99-$29.99/release | Selective releases, established artists |
| CD Baby | $9.95-$49 one-time | Long-term catalog, never expires |
| Ditto Music | $19/year | Budget-conscious, international |

All deliver to 150+ platforms (Spotify, Apple Music, YouTube Music, Tidal, Deezer, TikTok, Instagram).

---

### Other Release References

| File | Purpose |
|------|---------|
| **[Platform Comparison](../reference/release/platform-comparison.md)** | Feature matrix for each streaming platform |
| **[Metadata by Platform](../reference/release/metadata-by-platform.md)** | Required fields for each distributor/platform |
| **[Rights & Claims](../reference/release/rights-and-claims.md)** | Copyright, publishing, Content ID, licensing |

---

## Configuration & Overrides System

### Override System Documentation

**[How to Customize](../reference/overrides/how-to-customize.md)**

The override system lets you personalize behavior without modifying plugin files. Customizations survive updates.

**What can be overridden:**
- `CLAUDE.md` - Workflow instructions, general behavior
- `pronunciation-guide.md` - Artist/album-specific pronunciations
- `explicit-words.md` - Custom explicit content word list
- `lyric-writing-guide.md` - Lyric style, vocabulary, themes
- `suno-preferences.md` - Genre mappings, vocal preferences
- `album-planning-guide.md` - Track counts, structure preferences
- `album-art-preferences.md` - Visual style, color palette
- `mastering-presets.yaml` - Custom genre EQ/dynamics

**Default location:** `{content_root}/overrides/` (recommended) or set custom location in config.

**Merge behavior:** `CLAUDE.md` supplements (additive); pronunciation guide merges (custom takes precedence); other overrides add context to skill behavior.

---

**[Override Index](../reference/overrides/override-index.md)**

Detailed descriptions and examples for each override file.

---

## Quick Start Guides

**[First Album](../reference/quick-start/first-album.md)**

Complete walkthrough for creating first album in 2-4 hours (6-track EP):

**Phase 1: Setup** (5 min) - Run `/new-album`, verify creation
**Phase 2: Planning** (30-60 min) - Work through 7 planning phases
**Phase 3: Writing** (1-2 hours) - Create track files, write lyrics, run lyric review
**Phase 4: Suno Generation** (1-2 hours) - Copy to Suno, generate variations, log results
**Phase 5: Mastering** (20-30 min) - Analyze and master audio
**Phase 6: Release** (30 min) - Set up distributor account, upload

---

**[True-Story Album](../reference/quick-start/true-story-album.md)**

Extended workflow for documentary/research-based albums with source verification.

---

**[Bulk Releases](../reference/quick-start/bulk-releases.md)**

Strategy for releasing multiple albums/singles efficiently.

---

## Sheet Music System

**[Sheet Music Workflow](../reference/sheet-music/workflow.md)**

Convert audio to sheet music and create printable songbooks:

**Prerequisites:**
- Mastered WAV files
- Python 3.8+, dependencies: music21, librosa, pygame (audio playback)

**Output:** PDF songbooks with lyrics, notation, chord charts

---

**[Genre Recommendations](../reference/sheet-music/genre-recommendations.md)**

Genre-specific notation preferences (key signatures, time signatures, articulations).

---

**[Troubleshooting](../reference/sheet-music/troubleshooting.md)**

Common transcription issues and fixes.

---

## Cross-Platform & Cloud

**[WSL Setup Guide](../reference/cross-platform/wsl-setup-guide.md)**

Windows Subsystem for Linux configuration for audio tools.

---

**[Tool Compatibility Matrix](../reference/cross-platform/tool-compatibility-matrix.md)**

Ffmpeg, SoX, Python library compatibility across macOS, Linux, Windows.

---

**[Cloud Setup Guide](../reference/cloud/setup-guide.md)**

Cloudflare R2 and AWS S3 configuration for uploading promo videos.

---

## Developer Documentation

### Contributing

**[CONTRIBUTING.md](../../CONTRIBUTING.md)**

Complete development workflow:

**Branch model:**
- `develop` - active development, receives feature PRs
- `main` - stable releases only

**Development process:**
1. Create feature branch from `develop` (naming: `feat/`, `fix/`, `docs/`, `chore/`)
2. Make changes following existing patterns
3. Run `/bitwize-music:test all` locally
4. Commit with Conventional Commits format
5. Create PR targeting `develop` (not `main`)
6. Automated checks run (JSON/YAML validation, version sync, SKILL.md structure)

**Adding a new skill requires updating:**
- Create `/skills/your-skill/SKILL.md`
- Update `CLAUDE.md`, `help/SKILL.md`, `SKILL_INDEX.md`
- Update `CHANGELOG.md` under "Unreleased"
- Update `reference/model-strategy.md`, `README.md` if applicable
- Run tests, verify skill appears in help

---

### Testing

**[TESTING.md](../../TESTING.md)**

Complete testing plan with three phases:

**Phase 1: Fresh Install**
- Clone install from GitHub
- Plugin install via marketplace

**Phase 2: Configuration**
- Initial config setup
- Path resolution verification

**Phase 3: Core Workflow**
- Tutorial skill testing
- Album creation workflow
- Skill invocation tests

---

### Documentation Structure

**[docs/configuration.md](../../docs/configuration.md)**

Quick reference for `~/.bitwize-music/config.yaml` structure and path variables.

---

**[docs/skills.md](../../docs/skills.md)**

52 skills organized by category: Core Production, Research System, Quality Control, Release & Distribution, Album Management, Setup & Maintenance.

---

**[docs/troubleshooting.md](../../docs/troubleshooting.md)**

Common issues and solutions for setup, skills, workflows.

---

## Important Crosscutting Themes

### The 14 Production Gates

The plugin enforces 14 checkpoints before releasing an album:

**Pre-Writing:**
1. 7 planning phases complete
2. Album README documented

**Pre-Generation:**
3. Lyrics written and reviewed
4. Pronunciation checked
5. Prosody verified (lyric-reviewer)
6. AI-sounding patterns checked (voice-checker)
7. Explicit content flagged correctly (explicit-checker)
8. Style prompts created (suno-engineer)
9. All metadata complete (pre-generation-check)

**Pre-Release:**
10. Audio mastered to -14 LUFS
11. Album art in correct locations
12. Promo videos generated (optional but recommended)
13. Metadata uploaded to distributor
14. Final QA passed (release-director)

**Skipping any gate risks generating poor music or failing distribution.**

---

### Model Tier Strategy

**6 Opus 4.5 skills** (music-defining output, highest error cost):
- lyric-writer, suno-engineer, album-conceptualizer, lyric-reviewer, researchers-legal, researchers-verifier

**30 Sonnet 4.5 skills** (reasoning, coordination, moderate creativity):
- Most coordination and decision-making tasks

**15 Haiku 4.5 skills** (pattern matching, rule-based operations):
- File operations, environment detection, static information

This distribution reserves expensive models for tasks where quality directly impacts music or where errors have legal/credibility consequences.

---

### Path Structure (Mirrored)

Content and audio use **identical mirrored path structures** with artist and genre folders:

```
{content_root}/artists/{artist}/albums/{genre}/{album}/   # Markdown files
{audio_root}/artists/{artist}/albums/{genre}/{album}/     # WAV files
{documents_root}/artists/{artist}/albums/{genre}/{album}/ # PDFs (not in git)
```

**Common mistake:** Forgetting the `{artist}/` folder after the root. Audio paths need it too.

---

## Using This Documentation

### For Album Setup

1. **Start:** `reference/quick-start/first-album.md`
2. **Plan:** `reference/workflows/album-planning-phases.md`
3. **Write:** Leverage skill SKILL.md docs for specialized work
4. **Generate:** `reference/suno/` (V5 Best Practices + Pronunciation Guide + Structure Tags)
5. **Polish:** `reference/mastering/mastering-workflow.md`
6. **Release:** `reference/release/` and `reference/promotion/`

### For Reference Lookup

- **"What's the Suno rule for X?"** → `reference/suno/README.md` navigation table
- **"How do I pronounce Y in Suno?"** → `reference/suno/pronunciation-guide.md` homograph table
- **"Which skill for Z?"** → `reference/SKILL_INDEX.md` decision tree
- **"What audio settings for genre?"** → `reference/mastering/genre-specific-presets.md`
- **"How to distribute?"** → `reference/release/distributor-guide.md`

### For Plugin Development

1. **Contributing workflow:** `CONTRIBUTING.md`
2. **Testing strategy:** `TESTING.md`
3. **Model tier rationale:** `reference/model-strategy.md`
4. **New skill checklist:** `CONTRIBUTING.md` "Adding a New Skill" section

---

## Summary Stats

| Category | Count | Scope |
|----------|-------|-------|
| Suno guides | 11 | V5 prompting, pronunciation, tags, tips |
| Workflow guides | 6 | Album planning, status tracking, verification, error recovery |
| Mastering refs | 4 | Audio processing, loudness, EQ, genre presets |
| Release refs | 4 | Distribution, platforms, rights, metadata |
| Quick starts | 3 | First album, true-story, bulk releases |
| Cross-platform | 3 | WSL, compatibility, cloud setup |
| Overrides | 2 | Customization guide and index |
| Top-level | 5 | Skills index, state schema, terminology, distribution, model strategy |
| Dev docs | 3 | Contributing, testing, skills overview |
| Sheet music | 3 | Workflow, genre recommendations, troubleshooting |
| **Total** | **44+ files** | **All reference material across plugin** |

---

## Key Takeaways for Album Workflow Setup

1. **Always start with the 7 planning phases** before writing — they prevent foundation problems
2. **Suno V5 is different** — write new prompts, 4-7 descriptors, avoid "prompt fatigue"
3. **Phonetic spelling is mandatory** for homographs — never trust context (lyve, led, read, close, tear, bass, etc.)
4. **Status tracking gates are real** — album can't advance past "Research Complete" with unverified sources
5. **Mastering is automated** — -14 LUFS is non-negotiable for streaming; tinny tracks need high-mid cuts
6. **All paths need artist folder** — both content and audio use mirrored `{artist}/{genre}/{album}/` structure
7. **Distributor matters less than you'd think** — all deliver to 150+ platforms; pick by pricing model
8. **Override system is optional but powerful** — start with pronunciation guide, add CLAUDE.md if you have workflow preferences
9. **Model tiers are strategic** — Opus for music, Sonnet for coordination, Haiku for operations
10. **The plugin enforces gates for a reason** — skipping QC checkpoints results in poor audio or distribution failures

---

## File Locations Reference

**Suno guides:** `/reference/suno/` (11 files)
**Workflows:** `/reference/workflows/` (6 files)
**Mastering:** `/reference/mastering/` (4 files)
**Release:** `/reference/release/` (4 files)
**Quick start:** `/reference/quick-start/` (3 files)
**Overrides:** `/reference/overrides/` (2 files)
**Top-level:** `/reference/` (5 files: SKILL_INDEX.md, state-schema.md, terminology.md, distribution.md, model-strategy.md)
**Dev docs:** `/` (CONTRIBUTING.md, TESTING.md)
**Docs:** `/docs/` (configuration.md, skills.md, troubleshooting.md)

---

**Report Complete.** Last Updated: March 28, 2026
