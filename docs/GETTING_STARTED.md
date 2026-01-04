# Getting Started with TraderXice

> **What’s New:**
> - Dynamic user-defined session fields (YAML-driven)
> - Vault path is cached and auto-restored in the GUI
> - Session folders are sanitized for Obsidian/URL safety
> - Sessions are selectable and open the correct vault in Obsidian

## Installation & Setup

### 1. Install Dependencies
```bash
npm install
```

### 2. Build the Project
```bash
npm run build
```

### 3. Create Your First Session

```bash
# Using npx (development)
npx tsx src/cli/index.ts new-session --vault /path/to/vault

# Or after building
node dist/cli/index.js new-session --vault /path/to/vault
```
> You will be prompted for all fields defined in your `.traderxice/templates/trade-fields.yaml`.

## Command Reference

### Create a New Session

```bash
traderxice new-session [options]
```


**Options:**
- `-t, --type <type>` - Session type: `backtest`, `paper`, `live`, `propfirm` (default: backtest)
- All other fields are prompted dynamically from your YAML template
- `-v, --vault <path>` - Path to Obsidian vault (default: current directory)

**Examples:**

```bash
# Simple backtest session
npx tsx src/cli/index.ts new-session --strategy "Scalping" --vault ./my-vault

# Full details for a live trading session
npx tsx src/cli/index.ts new-session \
  --type live \
  --strategy "Breakout Strategy" \
  --pair GBPUSD \
  --timeframe 1H \
  --vault ~/Documents/TradingVault

# Prop firm trading session
npx tsx src/cli/index.ts new-session \
  --type propfirm \
  --strategy "Trend Following" \
  --pair XAUUSD \
  --timeframe 4H \
  --vault ~/Obsidian/Trading
```

## What Gets Created

When you create a session, TraderXice generates:

```
YourVault/
└── [SessionType]/              # e.g., "Backtesting", "Live Trading"
  └── [Strategy]/             # (sanitized)
    ├── README.md           # Strategy description (auto-created once)
    └── YYYY-MM-DD_[Name]/  # Session folder (sanitized)
      ├── session.md      # Main journal with all user fields in frontmatter
      ├── trades/         # Folder for trade logs
      └── screenshots/    # Folder for chart images
```

### Session.md Structure

Each `session.md` file includes:
- **Frontmatter** with metadata (type, strategy, date, pair, timeframe, status)
- **Session Information** section
- **Pre-Session Plan** section
- **Trades** section (for linking to trade logs)
- **Post-Session Review** section
- **Screenshots** section

### Strategy README

Each strategy gets an auto-generated README with sections for:
- Strategy Overview
- Entry Criteria
- Exit Criteria
- Risk Management
- Performance Notes
- Sessions (for linking to session folders)

## Development Workflow

### Run in Dev Mode
```bash
npm run dev -- new-session --strategy "Test" --vault ./test-vault
```

### Build
```bash
npm run build
```

### Clean Build
```bash
npm run clean
npm run build
```

## Testing

Create a test vault to experiment:

```bash
mkdir test-vault
npx tsx src/cli/index.ts new-session --strategy "Test" --vault ./test-vault
```

View the structure:
```bash
tree /F /A test-vault  # Windows
tree -L 3 test-vault   # macOS/Linux
```

## Integration with Obsidian

1. **Point to Your Vault**: Use `--vault` to specify your Obsidian vault path (GUI auto-caches last used)
2. **Open in Obsidian**: Once created, open the vault in Obsidian (GUI: use the session radio selector)
3. **Navigate to Session**: Go to the created session folder
4. **Start Journaling**: Edit `session.md` to document your trading

## Tips

- **Consistent Naming**: Use clear, descriptive strategy names (they are sanitized for safety)
- **Session Organization**: Sessions are automatically organized by type and strategy
- **Dynamic Fields**: All fields in your YAML template are prompted and stored
- **Vault Path Cache**: GUI remembers your last vault path
- **Session Selection**: Use the radio selector to open the correct session’s vault
- **Multiple Sessions**: Create multiple sessions per day for different strategies
- **README Reuse**: Strategy READMEs are created once and shared across sessions

## What's Next?

Phase 1 is complete! Coming soon:
- **Custom Templates**: Define your own journal structure
- **Export Tools**: Export to CSV, Excel, JSON
- **File Watcher**: Auto-create sessions from trigger files
- **Obsidian Plugin**: Create sessions directly from Obsidian

See `/docs/architecture-planning.md` for the full roadmap and `/docs/ELECTRON_GUI.md` for GUI details.
## Troubleshooting

- **Vault path not remembered?**
  - Make sure you are using the Electron GUI; vault path is cached in localStorage.
- **Session not selectable or not opening correct vault?**
  - Use the radio selector in the dashboard; only sanitized folders are used.
- **Dynamic fields not appearing?**
  - Check your `.traderxice/templates/trade-fields.yaml` for correct field definitions.
