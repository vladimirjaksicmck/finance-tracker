# Finance Tracker Project

## What this is
A personal finance tracker — single HTML file, fully encrypted, installable as a PWA with Google Drive cross-device sync.

## Project files
- `finance-tracker.html` — the full app (HTML + CSS + JS, single file)
- `manifest.json` — PWA manifest
- `service-worker.js` — offline caching (cache name: `finance-tracker-v11`); Google API URLs are excluded from the cache
- `icon.svg` — home screen icon (green, dollar sign)
- `index.html` — redirect entry point (meta refresh → finance-tracker.html)
- `CLAUDE.md` — this file

## Hosting
- Platform: GitHub Pages
- Repository: vladimirjaksicmck/finance-tracker
- Live URL: https://vladimirjaksicmck.github.io/finance-tracker/finance-tracker.html
- Entry point: https://vladimirjaksicmck.github.io/finance-tracker/ (redirects via index.html)

## Google Drive OAuth
- Client ID: `416374750017-aksd980ko28626cc9bn008ha2r61rbga.apps.googleusercontent.com`
- Authorised redirect URI registered in Google Cloud Console:
  `https://vladimirjaksicmck.github.io/finance-tracker/finance-tracker.html`
- Flow: OAuth 2.0 implicit (`response_type=token`); token + expiry stored in localStorage

---

## Current feature set

### Core app
- Single-file HTML app — AES-256 / PBKDF2 encryption; all data stored encrypted in localStorage
- Password lock screen: setup on first run, login on return, restore-from-backup flow
- Live password criteria (5 rules, red→green ticks), eye-toggle on every password field
- 8-card wallet summary strip (Income, Expenses, Net Balance, Entries, Cash, Cash Savings, Investments, Total Wealth)
- 12 tabs: Add, Log, Recurring, Budgets, Goals, Analytics, YoY, Net Worth, Summary, Calendar, Charts, Settings
- Dark mode toggle, persists across sessions

### Quick Capture
- Numpad entry + category/wallet pills
- **📋 Paste SMS button**: parses ZKB card SMS format, auto-fills CHF amount and beneficiary into amount display and Note field
- **Transaction date picker**: native `<input type="date">`, defaults to today, resets after each save; date stored on every transaction record

### Full Entry
- Description, amount, type, category, wallet, date, note
- Split transactions across multiple categories

### Transaction Log
- Sorted by transaction date descending (display-only; fallback to creation timestamp for older entries)
- Filter by type, wallet, category
- **Edit button (✏️)** on every entry: pre-filled modal for amount, type, category, wallet, note, date; Save commits and refreshes all views
- Delete button with immediate removal

### Recurring transactions
- Monthly / weekly / yearly schedules
- Apply Due button — walks forward from start date and creates all outstanding entries

### Budgets & Goals
- Budgets with colour-coded progress bars
- Goals with progress and days-left countdown

### Analytics & reporting
- Analytics tab, Year-on-Year comparison, Net Worth trend chart, monthly Summary report
- Calendar view (5 years back, click day for breakdown)
- Charts: income vs expenses bar, wallet donut, expenses-by-category horizontal bar

### Settings
- 100+ currencies by region
- Opening balances per wallet
- Custom income/expense categories
- Change password
- **Export Encrypted Backup** (`.enc`) — works on iOS Safari via data-URL fallback
- **Import Encrypted Backup** — file picker (`.enc`), inline password prompt, supports `{salt,data}` and legacy raw formats
- **Export CSV**

### Google Drive sync
- ☁ cloud icon in header bar — green = synced, orange = syncing, red = error, grey = not connected
- Settings → Google Drive Sync card: Connect, Sync Now, Disconnect, auto-sync toggle, last-synced timestamp
- On first connect: detects existing Drive backup and shows modal — "Restore from Drive" or "Overwrite with Local"
- **Cross-device decrypt**: password stored in `sessionStorage` at unlock; `_downloadAndDecrypt()` tries same-device `sessionKey` first, then re-derives with `sessionPw + backupSalt` seamlessly; password modal as last resort
- **Sync Now**: downloads Drive backup, compares `lastModified` timestamps, applies whichever is newer; shows explicit success message
- **Auto-sync**: fires on every `saveData()` call when token is valid; pre-upload guard aborts if 0 transactions
- **Upload verification**: after PATCH/POST, fetches `files/{id}?fields=size`; errors if ≤ 1000 bytes
- **Restore flow**: `_applyDriveData` uses backup's own salt (no new random salt), writes localStorage keys explicitly, shows "Restore Complete — X transactions loaded" modal, then `window.location.reload()`
- Encrypted backup format: `{ salt, data }` JSON — same format as manual `.enc` export

### PWA
- Installable on iOS (Add to Home Screen) and Android
- Offline-first via service worker; Google API URLs bypassed from cache

---

## Roadmap — next steps
1. **Android app via Capacitor** — wrap the HTML app as a native Android APK
2. **Google Play publication** — publish to Play Store
3. **Monetization tiers**
   - Free: core features
   - Premium — $3.99 one-time: unlock advanced analytics / charts
   - Premium+ — $1.49/month: Google Drive sync + priority support
