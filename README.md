# The Vault

Personal PWA movie and TV browser. Clean alternative to Plex's native UI — search, filter, ratings, poster art, and direct playback into Plex and Jellyfin.

**Live:** https://jzmovies.netlify.app (password protected)

---

## Architecture

- Single-file app: `index.html` (Movies) + `tv.html` (TV Shows)
- Data: `movies.json` (196 titles) + `tvshows.json` (49 shows) — all with `jellyfinId` pre-baked
- Hosting: Netlify — auto-deploys on every GitHub push to main
- No backend, no server, no database — pure static HTML/JS
- PWA — installable on iPhone home screen, runs in WKWebView

---

## Media Servers

| Item | Value |
|------|-------|
| Plex Server | 192.168.0.100:32400 |
| Plex Movies Section | 9 |
| Plex TV Section | 60 |
| Jellyfin Server | 192.168.0.100:8096 |
| Jellyfin Movies Indexed | 235 |
| Jellyfin TV Shows | 49 |

---

## Button Behavior

| Button | Target | Method | Why |
|--------|--------|--------|-----|
| Plex | app.plex.tv search | `_blank` | Breaks out of WKWebView → real Safari (logged into Plex) → Plex app launches |
| Jellyfin | Direct movie/show page | `_self` | Stays in PWA container, opens direct page |

---

## File Structure

| File | Purpose |
|------|---------|
| `index.html` | Movies page — full app, single file |
| `tv.html` | TV Shows page — full app, single file |
| `movies.json` | 196 movies with all metadata + jellyfinId |
| `tvshows.json` | 49 shows with all metadata + jellyfinId |
| `refresh_plex.py` | Data refresh script — runs locally on Windows PC |
| `netlify.toml` | Netlify config — no-cache headers for HTML and JSON |

---

## Data Refresh

Run `refresh_plex.py` on the Windows PC after any library change:
- Rescans Plex, rebuilds both JSON files including Jellyfin ID lookup
- Pushes to GitHub — Netlify auto-deploys
- Does NOT touch `index.html` or `tv.html`

---

## Key Design Decisions

- **Mixed content blocking:** HTTPS PWA cannot fetch HTTP (Jellyfin) at runtime. All Jellyfin IDs pre-baked into JSON at refresh time.
- **Plex deep links:** `plex://preplay` broken in 2025/2026 Plex rewrite. Workaround: `app.plex.tv` search surfaces correct movie at top.
- **`_blank` vs `_self`:** Plex needs `_blank` to hand off to real Safari session. Jellyfin needs `_self` to stay in PWA.
- **Apostrophes in titles:** Silently break inline `onclick`. Always use `addEventListener` after injection.

---

## Critical Rules

- **Sonnet only** — never use Opus on this project
- **Never touch modal CSS** — accepted as-is
- **Never touch layout** — ruled out
- **Never change Plex button from `_blank`**
- **Never change Jellyfin button from `_self`**
- **Never runtime-fetch Jellyfin** — HTTPS/HTTP mixed content blocked

---

## Open Items

- Run `refresh_plex.py` when library changes
- TMDB rating descriptor strings — deferred
- Favicon bold V design — not yet implemented

---

## Quick Reference

| Task | How |
|------|-----|
| Update library | Run `refresh_plex.py` on Windows PC |
| Force redeploy | Push any change to GitHub main |
| Plex admin | 192.168.0.100:32400/web |
| Jellyfin admin | 192.168.0.100:8096/web |
| Live commit | abe40f7b |

---

*Credentials in private doc — never commit to repo.*
