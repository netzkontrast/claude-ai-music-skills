# AI Music Skills Catalog

A comprehensive inventory of all 50 specialized skills in the bitwize-music Claude Code plugin, organized by function and documented with dependencies, workflows, and customization guidance.

---

## Summary

- **Total Skills**: 50
- **Categories**: 8 major functional areas
- **Creation Date**: 2026-03-28
- **Plugin Version**: See `.claude-plugin/plugin.json`

This catalog serves as a reference for understanding the plugin's architecture, discovering which skill to use for a given task, and understanding how skills interconnect.

---

## Skills by Category

### Creation & Planning (6 skills)

| Skill | Purpose | Key Dependencies | Slash Command |
|-------|---------|------------------|----------------|
| **new-album** | Create new album directory structure with templates | None | `/bitwize-music:new-album <name> <genre>` |
| **album-conceptualizer** | Design 7-phase album concept, tracklist, and narrative arc | new-album | `/bitwize-music:album-conceptualizer` |
| **album-ideas** | Track, manage, and organize brainstormed album ideas | None | `/bitwize-music:album-ideas list\|add\|remove` |
| **lyric-writer** | Write/revise lyrics with professional prosody, rhyme craft, and quality checks | album-conceptualizer | `/bitwize-music:lyric-writer <track-path>` |
| **suno-engineer** | Construct Suno V5 style prompts and optimize generation settings | lyric-writer | `/bitwize-music:suno-engineer <track-path>` |
| **album-art-director** | Design visual concepts and generate AI art prompts | album-conceptualizer | `/bitwize-music:album-art-director` |

### Research & Fact-Checking (11 skills)

| Skill | Purpose | Key Dependencies | Slash Command |
|-------|---------|------------------|----------------|
| **researcher** | Conduct investigative-grade research with triple-verification | album-conceptualizer | `/bitwize-music:researcher <topic>` |
| **researchers-biographical** | Research personal backgrounds and biography | researcher | (Internal; auto-invoked) |
| **researchers-financial** | Research SEC filings and financial data | researcher | (Internal; auto-invoked) |
| **researchers-gov** | Research DOJ, FBI, SEC press releases | researcher | (Internal; auto-invoked) |
| **researchers-historical** | Research archives and historical timelines | researcher | (Internal; auto-invoked) |
| **researchers-journalism** | Research investigative journalism articles | researcher | (Internal; auto-invoked) |
| **researchers-legal** | Research court documents and legal filings | researcher | (Internal; auto-invoked) |
| **researchers-primary-source** | Research subject's own words (tweets, blogs, forums) | researcher | (Internal; auto-invoked) |
| **researchers-security** | Research malware analysis and CVE databases | researcher | (Internal; auto-invoked) |
| **researchers-tech** | Research project histories and changelogs | researcher | (Internal; auto-invoked) |
| **researchers-verifier** | Quality control and fact-checking for research | researcher | (Internal; auto-invoked) |

### Quality Control & Review (8 skills)

| Skill | Purpose | Key Dependencies | Slash Command |
|-------|---------|------------------|----------------|
| **lyric-reviewer** | Pre-generation QC gate with 14-point checklist | lyric-writer, pronunciation-specialist | `/bitwize-music:lyric-reviewer <track-path>` |
| **pronunciation-specialist** | Scan for pronunciation risks and phonetic fixes | lyric-writer | `/bitwize-music:pronunciation-specialist <track-path>` |
| **explicit-checker** | Scan lyrics for explicit content and verify flags | lyric-writer | `/bitwize-music:explicit-checker <album-path>` |
| **plagiarism-checker** | Check lyrics for unintentional phrase matching | lyric-writer | `/bitwize-music:plagiarism-checker <album-name>` |
| **voice-checker** | Review lyrics and prose for AI-written patterns | lyric-writer, promo-writer | `/bitwize-music:voice-checker <path>` |
| **pre-generation-check** | Final validation gate before Suno generation (6 gates) | lyric-reviewer, pronunciation-specialist | `/bitwize-music:pre-generation-check <album>` |
| **validate-album** | Validate album structure and file integrity | None | `/bitwize-music:validate-album <album-name>` |
| **verify-sources** | Capture human source verification for tracks | researcher | `/bitwize-music:verify-sources <album-name>` |

### Production & Audio (5 skills)

| Skill | Purpose | Key Dependencies | Slash Command |
|-------|---------|------------------|----------------|
| **mix-engineer** | Polish raw Suno audio with per-stem processing | import-audio | `/bitwize-music:mix-engineer <album>` |
| **mastering-engineer** | Master audio to -14 LUFS for streaming platforms | import-audio | `/bitwize-music:mastering-engineer <album>` |
| **sheet-music-publisher** | Convert mastered audio to sheet music and songbooks | mastering-engineer | `/bitwize-music:sheet-music-publisher <album>` |
| **import-audio** | Import WAV/MP3 files and stems to correct locations | new-album | `/bitwize-music:import-audio <file> <album>` |
| **import-art** | Place album artwork in audio and content folders | album-art-director | `/bitwize-music:import-art <file> <album>` |

### File Management & Utilities (5 skills)

| Skill | Purpose | Key Dependencies | Slash Command |
|-------|---------|------------------|----------------|
| **import-track** | Move track markdown files to album location | new-album | `/bitwize-music:import-track <file> <album>` |
| **clipboard** | Copy track content (lyrics, prompts) to clipboard | lyric-writer | `/bitwize-music:clipboard <type> <album> <track>` |
| **rename** | Rename albums or tracks with path updates | None | `/bitwize-music:rename <type> <old> <new>` |
| **document-hunter** | Automate downloading documents from free sources | researcher | `/bitwize-music:document-hunter <case-name>` |
| **cloud-uploader** | Upload promo videos to Cloudflare R2 or AWS S3 | promo-director | `/bitwize-music:cloud-uploader <album>` |

### Promotion & Release (6 skills)

| Skill | Purpose | Key Dependencies | Slash Command |
|-------|---------|------------------|----------------|
| **promo-director** | Generate 15-second vertical promo videos (9:16) | mastering-engineer, album-art-director | `/bitwize-music:promo-director <album>` |
| **promo-writer** | Generate platform-specific social media copy | lyric-writer | `/bitwize-music:promo-writer <album>` |
| **promo-reviewer** | Review and iterate on social media copy | promo-writer | `/bitwize-music:promo-reviewer <album>` |
| **release-director** | Coordinate release: QA, distribution prep, uploads | mastering-engineer | `/bitwize-music:release-director <album>` |
| **cloud-uploader** | Upload promo videos to cloud storage | promo-director | `/bitwize-music:cloud-uploader <album>` |

### Workflow & Navigation (6 skills)

| Skill | Purpose | Key Dependencies | Slash Command |
|-------|---------|------------------|----------------|
| **session-start** | Run session startup: verify setup, load state, report status | None (runs at session start) | `/bitwize-music:session-start` |
| **resume** | Find album and show status with recommended next action | None | `/bitwize-music:resume <album-name>` |
| **next-step** | Analyze album state and recommend next action | resume | `/bitwize-music:next-step [album]` |
| **album-dashboard** | Show structured progress dashboard for album | resume | `/bitwize-music:album-dashboard <album>` |
| **tutorial** | Interactive guided album creation for new users | configure | `/bitwize-music:tutorial [new-album\|resume\|help]` |
| **help** | Show available skills and common workflows | None | `/bitwize-music:help` |

### System & Maintenance (3 skills)

| Skill | Purpose | Key Dependencies | Slash Command |
|-------|---------|------------------|----------------|
| **configure** | Set up or edit plugin configuration interactively | None (first-time setup) | `/bitwize-music:configure [setup\|edit\|show\|validate\|reset]` |
| **setup** | Verify Python environment and install dependencies | None (first-time setup) | `/bitwize-music:setup` |
| **skill-model-updater** | Update model references when new Claude models release | None | `/bitwize-music:skill-model-updater [check\|update\|update --dry-run]` |

### Development & Metadata (2 skills)

| Skill | Purpose | Key Dependencies | Slash Command |
|-------|---------|------------------|----------------|
| **test** | Run automated tests across 14 test categories | None | `/bitwize-music:test [all\|quick\|<category>]` |
| **ship** | Automate full code release pipeline (branch → PR → merge → release) | None | `/bitwize-music:ship "<conventional-commit-message>"` |

### Information & Reference (2 skills)

| Skill | Purpose | Key Dependencies | Slash Command |
|-------|---------|------------------|----------------|
| **about** | Display plugin version, creator info, and links | None | `/bitwize-music:about` |
| **genre-creator** | Create new genre documentation files | None | `/bitwize-music:genre-creator <genre-name>` |

---

## Workflow Connections

### Core Album Creation Flow

```
[User initiates]
  → session-start (status check)
  → new-album (create structure)
  → album-conceptualizer (7-phase planning)
    ├─ album-art-director (visual concept)
    └─ researcher (if true-story)
  → lyric-writer (write track lyrics)
    ├─ pronunciation-specialist (resolve pronunciation)
    ├─ suno-engineer (create style prompt — auto-invoked)
    └─ voice-checker (optional: check authenticity)
  → lyric-reviewer (QC gate)
  → pre-generation-check (final validation)
  → [Generate in Suno manually]
  → import-audio (import WAV files)
  → mix-engineer (optional: polish audio)
  → mastering-engineer (loudness normalization)
  → [Optional: album art generation → import-art]
  → [Optional: promo-director → promo videos]
  → promo-writer (generate social copy)
    └─ promo-reviewer (polish copy)
  → release-director (QA, distribution prep, upload)
  → [Live on platforms]
```

### Research-Heavy Album Flow (True-Story)

```
new-album (documentary flag)
  → researcher (main research coordination)
    ├─ researchers-legal (court documents)
    ├─ researchers-gov (DOJ/FBI/SEC)
    ├─ researchers-tech (project history)
    ├─ researchers-financial (SEC filings)
    ├─ researchers-journalism (news articles)
    ├─ researchers-security (malware/CVE)
    ├─ researchers-historical (archives)
    ├─ researchers-biographical (personal background)
    ├─ researchers-primary-source (subject's own words)
    ├─ document-hunter (automated doc download)
    └─ researchers-verifier (quality control)
  → verify-sources (human verification gate)
  → [Then continue with album creation flow above]
```

### Quick Resume & Status Flow

```
resume (find album, show status)
  → next-step (recommend action)
  → album-dashboard (visual progress)
  → [Then jump to relevant skill based on next step]
```

### Pre-Release Quality Check Flow

```
lyric-writer
  → pronunciation-specialist
  → lyric-reviewer
  → explicit-checker
  → plagiarism-checker
  → voice-checker (optional)
  → pre-generation-check (final gate)
```

### Release & Distribution Flow

```
mastering-engineer (audio ready)
  → [optional] promo-director (generate videos)
  → promo-writer (generate social copy)
  → promo-reviewer (polish copy)
  → cloud-uploader (upload promos — optional)
  → release-director (final QA, distribution, upload)
  → [Live on platforms]
```

---

## Skill Dependencies Map

### Hard Dependencies (Blocking)

Skills that have prerequisites (cannot run without):
- **album-conceptualizer**: Requires `new-album`
- **lyric-reviewer**: Requires `lyric-writer` + `pronunciation-specialist`
- **pre-generation-check**: Requires `lyric-writer` + `lyric-reviewer` + `pronunciation-specialist`
- **promo-director**: Requires `mastering-engineer` + `album-art-director`
- **cloud-uploader**: Requires `promo-director`
- **mastering-engineer**: Requires `import-audio`
- **mix-engineer**: Requires `import-audio`
- **sheet-music-publisher**: Requires `mastering-engineer`
- **release-director**: Requires `mastering-engineer`
- **album-dashboard**: Requires `resume`
- **tutorial**: Requires `configure`
- **session-start**: None (always available)

### Soft Dependencies (Recommended)

Skills that are most effective when used together:
- **album-art-director** + **import-art**: Generate and import artwork
- **lyric-writer** + **suno-engineer**: Auto-invoked sequence
- **lyric-writer** + **pronunciation-specialist** + **lyric-reviewer**: QC pipeline
- **promo-writer** + **promo-reviewer**: Copy generation and polish
- **researcher** + **document-hunter**: Research automation
- **promo-director** + **cloud-uploader**: Video generation and hosting

### No Dependencies (Can Run Anytime)

Skills that are independent and useful for any album:
- `new-album`
- `configure`, `setup`, `help`, `about`, `tutorial`
- `album-ideas`
- `clipboard`, `rename`, `validate-album`
- `skill-model-updater`, `test`, `ship`
- `genre-creator`

---

## Model Strategy

Skills are assigned Claude models strategically based on complexity:

| Model | Tier | Skills |
|-------|------|--------|
| **Claude Opus 4.6** | Highest capability | lyric-writer, suno-engineer, lyric-reviewer, album-conceptualizer, researcher, pre-generation-check (some), pronunciation-specialist |
| **Claude Sonnet 4.6** | Balanced | album-art-director, explicit-checker, document-hunter, promo-writer, promo-director, promo-reviewer, release-director, voice-checker, tutorial, session-start, verify-sources |
| **Claude Haiku 4.5** | Fast | import-track, import-audio, import-art, clipboard, help, about, new-album, rename, next-step, album-dashboard |

**Rationale**:
- **Opus**: Deep creative work (lyrics, research, prompting)
- **Sonnet**: Balanced tasks (processing, coordination, content generation)
- **Haiku**: Simple file operations and navigation

---

## Customization Notes

### Override Files (User Preferences)

The plugin supports override files in `{overrides}/` for deep customization without editing plugin files. See `CLAUDE.md` for setup.

#### Override Files by Skill Category

**Album Planning**:
- `album-planning-guide.md`: Track count preferences, structure, themes, duration

**Lyric Writing**:
- `lyric-writing-guide.md`: Style preferences, vocabulary, themes, custom rules
- `pronunciation-guide.md`: Artist names, album-specific terms, genre jargon (MERGE with base guide)
- `explicit-words.md`: Custom explicit word list (MERGE with base list)

**Suno Prompting**:
- `suno-preferences.md`: Genre mappings, default settings, words to avoid

**Production**:
- `mastering-presets.yaml`: Genre-specific EQ/dynamics targets, LUFS targets
- `mix-presets.yaml`: Genre-specific stem processing chains
- `sheet-music-preferences.md`: Page layout, notation, formatting rules

**Visual**:
- `album-art-preferences.md`: Style preferences, color palettes, composition rules

**Promotion**:
- `promotion-preferences.md`: Tone, platforms, messaging themes, hashtags, language, AI positioning
- `release-preferences.md`: QA requirements, platform priorities, timeline, metadata standards

**Research**:
- `research-preferences.md`: Source priority tiers, verification standards, research depth

**Workflow**:
- `CLAUDE.md`: Custom workflow instructions (MERGE with base CLAUDE.md from plugin)

### No Override Support

These skills don't support overrides (use defaults only):
- session-start, resume, next-step, album-dashboard, album-ideas
- tutorial, help, about, configure, setup
- clipboard, rename, validate-album, import-track, import-audio, import-art
- document-hunter, test, ship, skill-model-updater, genre-creator
- All researcher variants (use researcher base preferences)

---

## New User Onboarding Path

**For first-time users**, recommended initialization sequence:

1. **Setup** → `/bitwize-music:setup` (verify Python environment)
2. **Configure** → `/bitwize-music:configure` (set up workspace paths)
3. **Tutorial** → `/bitwize-music:tutorial new-album` (guided album creation)
4. **Album Ideas** (optional) → `/bitwize-music:album-ideas list` (track brainstorming)
5. **New Album** → `/bitwize-music:new-album <name> <genre>` (create structure)
6. **Album Conceptualizer** → `/bitwize-music:album-conceptualizer` (7-phase planning)
7. **Lyric Writer** → `/bitwize-music:lyric-writer <track>` (start writing)

For true-story albums, add:
- After step 4: **Researcher** → `/bitwize-music:researcher <topic>`
- After step 6: **Document Hunter** (optional) → `/bitwize-music:document-hunter <case>`
- Before generation: **Verify Sources** → `/bitwize-music:verify-sources <album>`

---

## Cross-Skill Communication Patterns

### Skill → Skill Handoffs

Where skills explicitly recommend invoking the next skill:

| From | To | When | Message |
|------|----|----|---------|
| lyric-writer | suno-engineer | After lyrics complete | Auto-invokes for style prompt |
| lyric-writer | pronunciation-specialist | Before review | User should run next |
| pronunciation-specialist | lyric-reviewer | After fixes applied | User should run next |
| lyric-reviewer | pre-generation-check | After review complete | User should run next |
| pre-generation-check | [Generate in Suno] | All gates pass | Instructions for manual generation |
| import-audio | mix-engineer | Audio imported | User can optionally run next |
| mix-engineer | mastering-engineer | Audio polished | User should run next |
| mastering-engineer | promo-director | Mastering complete | User can optionally run next |
| mastering-engineer | release-director | Audio ready | User should run next |
| promo-director | cloud-uploader | Videos ready | User can optionally run next |
| promo-writer | promo-reviewer | Copy generated | User should run next for polish |
| promo-reviewer | release-director | Promo ready | User should run next |
| album-conceptualizer | album-art-director | Concept complete | User can optionally run next |
| album-art-director | import-art | Concept designed | User should run after generation |

### Session Context Tracking

The `get_session()` MCP tool tracks:
- Last album worked on (auto-loaded for resume)
- Last skill used
- Pending actions
- User preferences

This enables seamless resumption via `resume` and `next-step` skills.

---

## Testing & Quality Assurance

### Built-in Test Categories

Run `/bitwize-music:test <category>`:

| Category | Checks | Command |
|----------|--------|---------|
| **config** | Configuration files and values | `/test config` |
| **skills** | SKILL.md frontmatter and requirements | `/test skills` |
| **templates** | Template file existence and structure | `/test templates` |
| **workflow** | Album workflow documentation | `/test workflow` |
| **suno** | Suno integration and V5 best practices | `/test suno` |
| **research** | Research standards and verification | `/test research` |
| **mastering** | Mastering presets and targets | `/test mastering` |
| **sheet-music** | Sheet music generation readiness | `/test sheet-music` |
| **release** | Release workflow completeness | `/test release` |
| **consistency** | Cross-reference and version sync | `/test consistency` |
| **terminology** | Consistent language and avoid deprecated terms | `/test terminology` |
| **behavior** | Scenario-based functionality tests | `/test behavior` |
| **quality** | Code quality and lint checks | `/test quality` |
| **all** (default) | All tests | `/test` or `/test all` |
| **quick** | Fast automated pytest suite | `/test quick` |

### Continuous Integration

The `ship` skill automates CI/CD:
- Creates feature branch
- Commits with conventional commit message
- Pushes and creates PR
- Waits for CI checks (fails if any check fails)
- Merges on success
- Bumps version (feat: MINOR, fix: PATCH, feat!: MAJOR)
- Pushes release commit (triggers auto-release.yml)

---

## Architecture Insights

### Skill Topology

**Layers** (bottom to top):
1. **Foundation**: configure, setup, session-start
2. **Utilities**: import-track, import-audio, import-art, clipboard, rename, validate-album
3. **Workflow Navigation**: resume, next-step, album-dashboard, help, about
4. **Core Creative**: new-album, album-conceptualizer, album-ideas, lyric-writer, album-art-director
5. **Specialized**: suno-engineer, pronunciation-specialist, lyric-reviewer, voice-checker, explicit-checker
6. **Research**: researcher + 10 researcher variants, document-hunter, verify-sources
7. **Production**: mix-engineer, mastering-engineer, sheet-music-publisher, import-audio/art
8. **Release**: promo-director, promo-writer, promo-reviewer, cloud-uploader, release-director
9. **Maintenance**: test, ship, skill-model-updater, genre-creator

### Decision Trees

Most users follow one of these journeys:

**Journey 1: Original Soundtrack (OST)**
- Album type: "Original Soundtrack"
- No research required
- Fast track to lyrics → generation → release

**Journey 2: True-Story Album**
- Album type: Documentary / Character Study
- Heavy research phase (researcher + specialists)
- Source verification gate (verify-sources)
- Then normal flow

**Journey 3: Album Remaster / Polish**
- Existing raw Suno output
- Skip album-conceptualizer
- Import audio → mix-engineer → mastering-engineer → release

**Journey 4: Promotion & Engagement**
- Album already released or in progress
- Focus on promo-director → promo-writer → social distribution

---

## Version & Compatibility

- **Supported Claude Models**: Opus 4.6, Sonnet 4.6, Haiku 4.5
- **Plugin Version**: Check `.claude-plugin/plugin.json` for current version
- **Python Requirements**: See `requirements.txt` for dependencies
- **Minimum Python**: 3.10+

### Model Compatibility

If running with older models:
- **Claude 3 Opus**: Most skills still work but may lack nuance
- **Claude 3 Sonnet**: Core features work; complex research/lyrics may degrade
- **Claude 3 Haiku**: Navigation skills only; avoid creative skills

---

## Glossary of Terms

| Term | Definition |
|------|-----------|
| **Album Status** | One of: Concept, Research Complete, Sources Verified, In Progress, Complete, Released |
| **Track Status** | One of: Not Started, Sources Pending, Sources Verified, In Progress, Generated, Final |
| **Suno V5** | Latest Suno AI music generation model (used for generation) |
| **LUFS** | Loudness Units Full Scale — standard streaming loudness (-14 LUFS target) |
| **Stems** | Individual instrument tracks from Suno (vocals, drums, bass, etc.) |
| **Override** | User-customized files in `{overrides}/` directory (preferences, custom rules) |
| **MCP** | Model Context Protocol — the server providing structured data access |
| **Pre-Generation Check** | The 6-gate validation before Suno generation |
| **Source Verification** | Human confirmation that research sources are real and accurate |

---

## Quick Reference: When to Use Each Skill

**"I want to..."**

- **...start a new album** → `new-album` then `album-conceptualizer`
- **...plan the album concept** → `album-conceptualizer`
- **...write lyrics** → `lyric-writer`
- **...check my lyrics before generation** → `lyric-reviewer` (then `pre-generation-check`)
- **...handle pronunciation** → `pronunciation-specialist`
- **...research a true story** → `researcher` (+ specialist researchers)
- **...generate Suno prompts** → `suno-engineer`
- **...master audio** → `mastering-engineer` (after import-audio)
- **...create album art** → `album-art-director`
- **...generate promo videos** → `promo-director` (after mastering + art)
- **...write social media copy** → `promo-writer`
- **...release my album** → `release-director`
- **...check my progress** → `album-dashboard` or `next-step`
- **...configure the plugin** → `configure` then `setup`
- **...find where I left off** → `resume`
- **...create sheet music** → `sheet-music-publisher` (after mastering)
- **...check for issues** → `validate-album` or `explicit-checker`
- **...upload to cloud** → `cloud-uploader` (after promo-director)

---

## Future Enhancements

Potential skills for future development:
- **Video Editor**: Trim/edit promo videos post-generation
- **Metadata Manager**: Manage ISRC codes, songwriter credits, publishing rights
- **Rights & Licensing**: Royalty tracking, license generation
- **Analytics**: Track streams, engagement, audience demographics
- **Collaboration Tools**: Multi-artist workflow support
- **Cover Art Manager**: Batch artwork generation with variations
- **Archival & Backup**: Automated backup to cloud storage
- **Licensing Database**: Integration with music licensing services

---

## Support & Documentation

For each skill, reference documentation is available:
- **SKILL.md**: Detailed skill documentation
- **Supporting Files**: Referenced in `CLAUDE.md` → Linked in each SKILL.md
- **Reference Directory**: `${CLAUDE_PLUGIN_ROOT}/reference/` for genre guides, Suno tips, etc.
- **MCP Server**: Provides structured data access (see CLAUDE.md for MCP tool list)

---

*This catalog is a living document. Skills and workflows evolve with plugin updates. For the latest information, see the plugin README and CONTRIBUTING guide.*
