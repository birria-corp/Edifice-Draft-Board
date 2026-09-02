# Edifice Draft Board — Session Context

**Project:** Edifice Draft Board
**Repo:** https://github.com/birria-corp/Edifice-Draft-Board
**Live:** https://birria-corp.github.io/Edifice-Draft-Board/
**Version:** 1.0
**Firebase Project:** zeptrack-f8720
**Stack:** Vanilla JS, Firebase ESM (Auth + Firestore), PWA

## File Structure
```
index.html       # Single-file app
sw.js            # Service worker
manifest.json    # PWA manifest
icon.svg         # Source SVG icon (dark green baseball diamond)
version.json     # {"version":"1.0"}
README.md
CONTEXT.md
```

## Active Features
- 89-player sample pool (MLB 2026); import any Claude-generated board via JSON
- Position eligibility: C, 1B, 2B, 3B, SS, OF, DH, SP, RP; multi-eligible supported
- Views: Players (filterable by position), Best Available, Sleepers, Roster
- Roster: 22 total = 16 active slots + 6 reserves
  Active: C, 1B, 2B, 3B, SS, OF1, OF2, OF3, DH, U, SP1, SP2, SP3, SP4, RP1, RP2
- Keeper tracking with budget deduction
- League Settings: season, totalBudget, trades, keeper editor, scoring (36 params)
- AI prompt generator (buildPrompt) with full league context, copies to clipboard
- Firebase Auth: Google signInWithPopup, onAuthStateChanged -> window._authUser + authChanged CustomEvent
- Firestore: users/{uid}/data/EDB_state -> {payload: JSON.stringify(state)}
- Conflict resolution: timestamp compare + confirm() dialog
- Font scaling: FS_STEPS=[75,85,92,100,108,116,125,135,146,158], applied as % on documentElement
- Auto-export: >4 days since EDB_lastexport triggers silent JSON export
- Version check: fetches version.json?_=timestamp, compares to APP_VERSION
- Settings modal: font A-/A+, version check, export, import
- Topbar: app title, gear -> settings modal, auth state (sign in / email + sync + sign out)
- Toast notifications: window._showToast(msg), 3s timeout
- PWA: sw.js registered, network-first for index.html/version.json, cache-first for assets

## Key Technical Decisions
- Single-file HTML (all CSS + JS inline)
- Firebase imported via ESM type="module" in <head>; app script is regular <script> in <body>
- window.* bridge: _signIn, _signOut, _syncNow, _authUser, _showToast, _reloadAppState
- authChanged CustomEvent dispatched after onAuthStateChanged fires
- Utility functions renamed: idd(p) (was id()), getp(n) (was get()) -- avoid naming conflicts
- prompt() renamed to buildPrompt() -- avoids shadowing global window.prompt
- ASCII hyphens only in button/template text -- Unicode minus silently breaks Babel
- Old key migration: edifice-draft-board-v1 -> EDB_state on first load
- localStorage keys prefixed EDB_: EDB_state, EDB_fontsize, EDB_lastexport
- state saved with ts:Date.now() for cloud conflict resolution
- saveState() replaces original save() to avoid naming conflict with potential future use

## Scoring Keys
single, double, triple, bb, ci, cs, csc, cycle, error, gdp, hbp, hr, ibb, ko, ofast, r, rbi, sb, sh,
balk, bs, cg, er, hold, inn, irs, k, loss, nh, pg, qs, save, shutout, winSp, winRp, wp

## Firebase Config
```js
apiKey: "AIzaSyCrTmJNohK-7_t2BHx70HA6AfLmZyryLUY"
authDomain: "zeptrack-f8720.firebaseapp.com"
projectId: "zeptrack-f8720"
storageBucket: "zeptrack-f8720.firebasestorage.app"
messagingSenderId: "791166858370"
appId: "1:791166858370:web:9ccf77cdad85cb3689b09b"
```

## Version Bump Checklist
- APP_VERSION in index.html
- CACHE_VERSION in sw.js (must match)
- version.json "version" field

## Packaging Convention
Zip name: `Edifice-Draft-Board-vX.X.zip`
Include: all repo files + updated CONTEXT.md

## Log
- 2026-09-02: v1.0 initial build — consolidated source into single-file PWA with Firebase sync
