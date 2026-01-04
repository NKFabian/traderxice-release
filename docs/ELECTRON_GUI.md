# TraderXice Electron GUI

## Quick Start


### Run in Development Mode
```bash
npm run dev:electron
```
> The app will remember your last selected vault path (local cache).

This will:
1. Build the Electron app from TypeScript
2. Copy HTML/CSS files
3. Launch the Electron window

### Build for Production
```bash
npm run package
```

This creates an installable application in the `release/` folder.

## Features


### Main Window
- **Vault Selection**: Browse and select your Obsidian vault folder (auto-cached)
- **Session Creation Form**: Create new trading sessions with a friendly GUI (dynamic fields from YAML)
- **Session Radio Selector**: Choose and open any session in your vault
- **Real-time Feedback**: See success/error messages immediately
- **Session Details**: View created session path and next steps


### Session Creation Fields
- **All fields** are defined in `.traderxice/templates/trade-fields.yaml` (dynamic, user-configurable)
- **Session Type** (required): Backtest, Paper, Live, or Prop Firm
- **Strategy Name** (required): Name of your trading strategy
- **Trading Pair** (optional): e.g., EURUSD, GBPUSD
- **Timeframe** (optional): e.g., 5M, 1H, 4H, 1D


### Dark Theme
- Modern VS Code-inspired dark theme
- Smooth animations and transitions
- Accessible form controls

## Architecture

### Process Structure
```
Main Process (main.ts)
  ├── Window Management
  ├── IPC Handlers
  └── SessionManager Integration
  
Preload Script (preload.ts)
  └── Secure API Bridge
  
Renderer Process (renderer.ts + index.html)
  ├── UI Logic
  ├── Form Handling
  └── Status Display
```


### IPC Communication
- `select-vault`: Opens folder dialog for vault selection
- `create-session`: Creates a new session via SessionManager
- `get-recent-sessions`: Retrieves all sessions for dashboard
- `open-obsidian`: Opens the selected session’s vault in Obsidian (auto-detects vault root)

## File Structure
```
src/electron/
├── main.ts              # Main process (Electron window)
├── preload.ts           # Preload script (security bridge)
└── renderer/
    ├── index.html       # UI layout
    ├── styles.css       # Styling
    └── renderer.ts      # UI logic
```

## Development Notes

### Hot Reload
Currently, you need to restart the app to see changes:
```bash
# Kill the running Electron process
# Then rebuild and run
npm run dev:electron
```

### Debugging
- DevTools automatically open in development mode
- Check console for renderer process logs
- Main process logs appear in terminal

### Building
The build process:
1. Compiles TypeScript to `dist-electron/`
2. Copies HTML/CSS files
3. Uses electron-builder to create installers

## Keyboard Shortcuts
- `Ctrl+R` / `Cmd+R`: Reload window
- `Ctrl+Shift+I` / `Cmd+Option+I`: Toggle DevTools
- `Alt+F4` / `Cmd+Q`: Quit application

## Common Issues

### "Cannot find module" errors
Run the build command first:
```bash
npm run build:electron
```

### Blank window
Check that HTML/CSS files were copied:
```bash
dir dist-electron\electron\renderer  # Windows
ls dist-electron/electron/renderer   # macOS/Linux
```

### Session not creating
1. Verify vault path is selected
2. Check console for errors
3. Ensure strategy name is filled


## Next Steps

Recent enhancements:
- [x] Dynamic session fields (YAML-driven)
- [x] Vault path cache (auto-restore last used)
- [x] Session radio selector and auto-selection
- [x] Session folder name sanitization
- [x] Correct vault root detection for Obsidian opening

Planned:
- [ ] Hot reload in development
- [ ] Session history viewer
- [ ] Quick session templates
- [ ] Settings panel
- [ ] Recent vaults list
- [ ] Drag & drop vault selection
## Troubleshooting

- **Vault path not remembered?**
  - Make sure you are using the Electron GUI; vault path is cached in localStorage.
- **Session not selectable or not opening correct vault?**
  - Use the radio selector in the dashboard; only sanitized folders are used.
- **Dynamic fields not appearing?**
  - Check your `.traderxice/templates/trade-fields.yaml` for correct field definitions.
