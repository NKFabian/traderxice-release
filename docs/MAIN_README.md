# TraderXice

Trading practice and journal management system integrated with Obsidian.

## Highlights
- Dynamic, YAML-driven fields (strategy → profile → global resolution)
- GUI and CLI both prompt every user-defined field
- Vault path cached and auto-restored (GUI)
- Session folder names are sanitized for Obsidian/URL safety
- Strategy-aware templates and hubs for trades, wins, losses, break-even
- Packaged app uses custom icons; dev builds include the in-app logo

## Quick Start

### Prerequisites
- Node.js 16+
- npm
- Obsidian (or a folder you use as a vault)

### Install
```bash
git clone https://github.com/NKFabian/traderxice.git
cd traderxice
npm install
```

### GUI (recommended)
```bash
npm run dev:electron
```
1) Browse to select your Obsidian vault
2) Fill session type, strategy, pair, timeframe, and dynamic fields
3) Create session → open vault → navigate to the new session

### CLI
```bash
npx tsx src/cli/index.ts new-session \
  --strategy "Scalping" \
  --type backtest \
  --pair EURUSD \
  --timeframe 5M \
  --vault /path/to/vault
```

## What Gets Created
```
YourVault/
 [SessionType]/                # Backtesting, Paper Trading, Live Trading, Prop Firm
     [Strategy]/               # Sanitized strategy folder
         README.md             # Strategy description
         YYYY-MM-DD_[Name]/    # Session folder (sanitized)
             session.md        # Main journal (frontmatter = all user fields)
             trades/           # Trade logs
             screenshots/      # Images and attachments
```

## Interfaces

### Desktop GUI
- Dark theme, live status, session list refresh
- Strategy-aware dynamic fields (YAML-driven)
- Vault selector with cache and Obsidian opener
- Profile apply/export/import

### Command Line
- Fast creation with full field prompts
- Good for automation/scripts

## Sessions & Trades
- Session frontmatter is the source of truth for metadata
- Outcome hubs (wins/losses/breakeven) capture graph links; example trades live in each folder
- Strategy README auto-created; type README auto-created if missing
- Templates also copied to `.templates/` for Obsidian’s Templates plugin

## Development & Packaging
```bash
# Build (core)
npm run build

# Build Electron (copies renderer + assets)
npm run build:electron

# Dev GUI
npm run dev:electron

# Package installer (icons auto-generated)
npm run package
```

## Documentation Map
- GUI Guide: docs/ELECTRON_GUI.md
- CLI & Getting Started: docs/GETTING_STARTED.md
- Architecture & Planning: docs/architecture-planning.md
- Graph View: docs/GRAPH_TREE_VIEW.md
- Metrics & Trade Management: docs/metrics-system.md, docs/TRADE_MANAGEMENT.md
- Documentation index: DOCUMENTATION_INDEX.md

## Roadmap Snapshot
- Template system expansion (manifests, validation, inheritance)
- Export system (JSON/CSV/Excel/HTML)
- File watcher, HTTP API, Obsidian plugin
- Cloud sync and analytics (future)

## Contributing
Pull requests welcome—see docs/architecture-planning.md for current direction.

## License
MIT © Fabian-Ivo Horvatic
