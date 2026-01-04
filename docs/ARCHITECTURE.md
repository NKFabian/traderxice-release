# TraderXice - Architecture & Planning

> **Philosophy**: Maximum flexibility. User is king. We provide structure, not constraints.

## Project Status

### Core Principles Implemented
- [x] Obsidian vault integration (markdown-based)
- [x] Multi-session type support (backtest, paper, live, prop firm)
- [x] User-defined templates and dynamic fields (YAML-driven)
- [x] Multiple interaction methods (CLI, Electron GUI)
- [x] Export flexibility (CSV, Excel, JSON, HTML, XML)
- [x] Cloud compatibility consideration (S3, etc.)
- [x] Vault path local cache (auto-restore last used vault)
- [x] Session folder name sanitization (Obsidian/URL safe)
- [x] Session radio selection and auto-selection after creation
- [x] Correct vault root detection for Obsidian opening

---

## Architecture Decisions


### 1. Session Creation - Multi-Interface Approach
**Decision**: Support ALL methods. Let users choose their workflow.

- [x] **CLI Interface**: `traderxice new-session` (now prompts for all dynamic fields)
- [x] **Electron GUI**: Desktop app with dynamic field form, vault cache, session selection
- [ ] **Obsidian Plugin**: Command palette + modal interface (planned)
- [ ] **File Watcher**: Drop trigger files to auto-create sessions (planned)
- [ ] **HTTP API**: REST endpoint for external tool integration (planned)
- [ ] **Configuration File**: Define sessions in YAML/JSON, run `traderxice sync` (planned)

**Core Principle**: Single engine, multiple frontends.

---

### 2. Template System - Users decides
**Decision**: User controls EVERYTHING about their journal structure.


#### Template Configuration Strategy
- [x] **Template Discovery**: Scan `.traderxice/templates/` folder
- [x] **Template Metadata**: Each template has a manifest (JSON/YAML)
- [x] **Variable Substitution**: Support placeholders like `{{date}}`, `{{strategy}}`, `{{pair}}`, and all user fields
- [ ] **Template Inheritance**: Allow templates to extend base templates (planned)
- [x] **Custom Fields**: Users define their own frontmatter fields (YAML-driven)
- [x] **Folder Structure**: Templates define which folders/files to create

#### Template Manifest Example
```yaml
# .traderxice/templates/my-backtest/template.yaml
name: "My Backtest Template"
type: backtest
description: "My custom backtesting setup"

variables:
  - name: strategy
    prompt: "Strategy name?"
    required: true
  - name: pair
    prompt: "Trading pair?"
    default: "EURUSD"
  - name: timeframe
    prompt: "Timeframe?"
    default: "1H"

structure:
  - path: "session.md"
    template: "session.md.template"
  - path: "trades/"
    type: "folder"
  - path: "screenshots/"
    type: "folder"
  - path: "analysis.md"
    template: "analysis.md.template"
  - path: "README.md"
    template: "readme.md.template"
```

**User Responsibility**: Define what their journal looks like.  
**TraderXice Responsibility**: Render templates and create structure.

---

### 3. Vault Organization - Strategy-First Hierarchy 📁

```
TradingVault/
├── .traderxice/                    # TraderXice configuration
│   ├── config.yaml                 # Global settings
│   ├── templates/                  # User-defined templates
│   │   ├── my-backtest/
│   │   ├── my-live-trading/
│   │   └── default-backtest/
│   └── exports/                    # Export configurations
│
├── Backtesting/
│   ├── README.md                   # Landing page (optional, user-defined)
│   ├── Strategy-A/
│   │   ├── README.md               # Strategy description (optional)
│   │   ├── 2025-12-13_Session1/
│   │   │   ├── session.md
│   │   │   ├── trades/
│   │   │   └── screenshots/
│   │   └── 2025-12-14_Session2/
│   └── Strategy-B/
│       └── README.md
│
├── Paper Trading/
│   ├── README.md
│   └── Strategy-C/
│
├── Live Trading/
│   └── README.md
│
└── Prop Firm/
    └── README.md
```


**Key Features**:
- [x] Strategy-based organization within each trading type
- [x] Optional README.md "landing pages" at each level (auto-generated)
- [x] User decides if/how to describe strategies
- [x] README generation (auto-created for new strategies)

---

### 4. Data Storage - Hybrid + Export Everything 💾

**Decision**: Store internally in flexible format, export to anything.

#### Internal Storage Strategy
- [x] **Frontmatter**: Session metadata in markdown files (all user fields, human-readable)
- [ ] **JSON Index**: `.traderxice/index.json` for quick lookups/queries (planned)
- [x] **Trade Logs**: User choice (CSV, JSON, or markdown tables)
- [x] **Attachments**: Images/screenshots in session folders

#### Export System (Cloud-Ready)
- [ ] **Export Formats**:
  - [ ] CSV (trades, performance metrics)
  - [ ] Excel (formatted workbooks with charts)
  - [ ] JSON (structured data for APIs)
  - [ ] HTML (web reports)
  - [ ] XML (legacy system compatibility)
- [ ] **Export Targets**:
  - [ ] Local filesystem
  - [ ] S3 / Cloud storage
  - [ ] HTTP POST (webhook)
  - [ ] Database (PostgreSQL, MongoDB)

#### Export Configuration
```yaml
# .traderxice/exports/s3-backup.yaml
name: "S3 Backup"
enabled: true
schedule: "daily"  # or "on-session-close"

format: json
target:
  type: s3
  bucket: "my-trading-data"
  region: "us-east-1"
  path: "traderxice/{{year}}/{{month}}/"

include:
  - session_metadata
  - trade_logs
  - performance_metrics
  - screenshots  # optional
```

**Recommendation for "Open It Up"**:
- **Internal**: JSON (structured, queryable, cloud-native)
- **User-facing**: Markdown (readable, editable, Obsidian-friendly)
- **Export**: Everything (let users choose)

---

## 🔧 Technical Architecture (To Implement)

### Core Components
- [x] **Session Manager**: Create, update, close sessions, sanitize names, store all user fields
- [x] **Template Engine**: Render templates with variable substitution, dynamic fields
- [ ] **Export Engine**: Convert data to multiple formats (planned)
- [ ] **Cloud Sync**: Upload to S3/cloud storage (planned)
- [x] **Vault Manager**: Interact with Obsidian vault structure, local cache, session selection
- [ ] **CLI Interface**: Command-line tool
- [ ] **Plugin Interface**: Obsidian plugin (separate repo?)
- [ ] **API Server**: HTTP API for external integrations

### Data Flow
```
User Input (CLI/Plugin/API/File)
    ↓
Session Manager (validates, processes)
    ↓
Template Engine (renders structure)
    ↓
Vault Manager (creates files/folders)
    ↓
Index Update (JSON index)
    ↓
Export Engine (optional, on trigger)
    ↓
Cloud Sync (optional, scheduled/immediate)
```

---

## 📦 Technology Stack (Proposed)

### Core
- [ ] **TypeScript**: Type safety, better DX
- [ ] **Node.js**: Cross-platform, rich ecosystem

### Template Engine
- [ ] **Handlebars** or **Nunjucks**: Powerful templating with logic
- [ ] **Gray-matter**: Parse frontmatter

### Data & Export
- [ ] **csv-writer**: CSV generation
- [ ] **exceljs**: Excel file creation
- [ ] **Marked** or **markdown-it**: Markdown parsing
- [ ] **AWS SDK** (optional): S3 integration

### CLI
- [ ] **Commander.js** or **Yargs**: CLI framework
- [ ] **Inquirer.js**: Interactive prompts

### Obsidian Plugin
- [ ] **Obsidian API**: Plugin development
- [ ] Separate repository or monorepo?

---

## 🎯 MVP Roadmap

### Phase 1: Core Foundation ✅ **COMPLETE**
- [x] Project setup (TypeScript, build system)
- [x] Basic session creation (CLI only)
- [x] Simple template system (one default template)
- [x] Markdown file generation
- [x] Folder structure creation
- [x] Frontmatter metadata support
- [x] Strategy README auto-generation
- [x] Multi-session type support (backtest, paper, live, propfirm)

**Files Created**:
- `src/cli/index.ts` - CLI interface with `new-session` command
- `src/core/SessionManager.ts` - Core session creation logic
- `src/utils/fs.ts` - File system utilities
- `src/utils/format.ts` - Formatting and template variable substitution
- `src/types.ts` - TypeScript type definitions

**Commands Available**:
```bash
traderxice new-session --strategy "Name" --type backtest --pair EURUSD --vault /path/to/vault
```

### Phase 1.5: Electron Desktop GUI ✅ **COMPLETE**
- [x] Electron application setup
- [x] Modern dark theme UI
- [x] Vault folder browser/selector
- [x] Session creation form with validation
- [x] IPC communication between main/renderer
- [x] Real-time feedback and status messages
- [x] Build and packaging scripts

**Files Created**:
- `src/electron/main.ts` - Main Electron process
- `src/electron/preload.ts` - Security bridge (contextBridge)
- `src/electron/renderer/index.html` - UI layout
- `src/electron/renderer/styles.css` - Dark theme styling
- `src/electron/renderer/renderer.ts` - UI interaction logic
- `tsconfig.electron.json` - Electron-specific TypeScript config
- `docs/ELECTRON_GUI.md` - GUI documentation

**Commands Available**:
```bash
npm run dev:electron    # Launch desktop app
npm run package         # Build installer
```

### Phase 2: Template System
- [ ] Template discovery from `.traderxice/templates/`
- [ ] Variable substitution
- [ ] Multiple template support
- [ ] Template validation

### Phase 3: Export System
- [ ] JSON export
- [ ] CSV export
- [ ] Export configuration

### Phase 4: Multi-Interface
- [ ] File watcher
- [ ] HTTP API
- [ ] Obsidian plugin (if desired)

### Phase 5: Cloud & Advanced
- [ ] S3 integration
- [ ] Scheduled exports
- [ ] Performance analytics
- [ ] Excel/HTML exports

---

## 🤔 Open Questions

### Technical
- [ ] **Monorepo vs Multi-repo**: Keep CLI and plugin together or separate?
- [ ] **Build System**: esbuild, tsc, or bundler?
- [ ] **Testing Strategy**: Jest, Vitest, or other?
- [ ] **Config Format**: YAML, JSON, or TOML?

### User Experience
- [ ] **Default Templates**: Should we ship with examples?
- [ ] **Session Naming**: Auto-generate or user-defined?
- [ ] **README Generation**: Auto-create strategy READMEs with prompts?
- [ ] **Migration Tools**: Import from other journal systems?

### Cloud Integration
- [ ] **Authentication**: How to handle S3 credentials securely?
- [ ] **Sync Strategy**: Incremental or full sync?
- [ ] **Conflict Resolution**: What if cloud and local diverge?

---

## 📝 Notes & Ideas

- Consider **plugin marketplace** for community templates
- Potential for **strategy backtesting integration** (future)
- **Mobile companion app** for logging trades on-the-go (future)
- **AI-powered insights** from journal data (future)

---

**Last Updated**: December 13, 2025  
**Status**: Phase 1 & 1.5 Complete - GUI and CLI Both Working!  
**Next Step**: Implement Phase 2 - User-defined template system
