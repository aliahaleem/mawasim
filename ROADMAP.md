# Mawasim — Roadmap & Future Recommendations

## Current State (v1 — March 2026)

Single self-contained HTML file with:
- Full 1447 AH Islamic calendar with occasions, duas (with audio), and preparation reminders
- Calendar Builder: import prayers, adhkar, fasts, Shawwal 6, daily inspiration, Eid al-Adha into Apple/Google/Outlook
- Deterministic UIDs so re-imports update existing events (Apple/Outlook)
- Shawwal tracker with strategic fasting tips
- Responsive two-column layout with sidebar + mobile FAB

---

## Short-Term Improvements

### 1. ICS Subscription URL (Priority: High)
**Problem:** Currently users download a `.ics` file and manually import it. If you change events (e.g. update a dua, fix a date), users must re-import manually.

**Solution:** Host the generated `.ics` file at a stable URL. Users subscribe once via:
- Google Calendar: Other calendars → From URL → paste link
- Apple Calendar: File → New Calendar Subscription → paste link

**How it works:**
- Calendar apps poll the URL every 12-24 hours and auto-sync changes
- You update the hosted file; users get changes automatically
- No re-import, no duplicates, no user action needed

**Implementation options:**
- **GitHub Pages:** Push the `.ics` file to a repo, serve via `https://username.github.io/mawasim/calendar.ics`
- **Cloudflare Workers / Vercel Edge:** Generate the `.ics` dynamically based on query params (e.g. `?prayers=1&fasts=1&city=London`)
- **Static hosting + build script:** Generate `.ics` at build time, deploy to any CDN

**Recommended first step:** Host a "default" `.ics` with the most common configuration on GitHub Pages. Add a "Subscribe" button alongside the current "Download" button.

### 2. Per-User Personalized Subscription URL
**Problem:** Different users want different selections (some want Tahajjud, some don't).

**Solution:** Encode user selections into the URL query string:
```
https://mawasim.app/cal.ics?prayers=1&adhkar=1&tahajjud=0&fasts=monthu,white&inspo=1&city=London&country=GB
```

A serverless function reads the params, generates the `.ics` on the fly, and returns it. The user subscribes to their personalized URL.

### 3. Multi-Year Date Support
**Problem:** All dates are hardcoded to 1447 AH (2025-2026). Next year requires manual updates.

**Solution:**
- Use a Hijri-Gregorian conversion library (e.g. `moment-hijri` or a lightweight custom one)
- Compute all occasion dates dynamically based on current Hijri year
- White Moon Days, Shawwal dates, Eid dates all auto-calculate
- Only need to verify/override dates when moon sighting differs from calculation

### 4. Push Notifications (PWA)
**Problem:** Calendar imports are passive — users may ignore calendar notifications.

**Solution:** Convert to a Progressive Web App (PWA) with:
- Service Worker for offline support
- Push notifications via Web Push API
- User opts in to specific notification categories
- Works on mobile home screen like a native app

---

## Medium-Term Features

### 5. Quran & Hadith Audio Library
Expand the daily inspiration feature:
- Full audio recordings of each dua and hadith
- Link from calendar events back to the app's audio player
- Offline caching via Service Worker

### 6. Community Features
- Share your Shawwal tracker progress
- Leaderboard for completed sunnahs (opt-in, anonymous)
- Family group: share a calendar subscription with household members

### 7. Location-Aware Prayer Times
- Use browser Geolocation API for automatic city detection
- Auto-update prayer times daily (rather than one-time fetch)
- Integrate with the subscription URL so calendar events have accurate per-day prayer times

### 8. Ramadan Mode
- Auto-activate during Ramadan with enhanced UI
- Daily Quran reading tracker (1 juz/day = complete in 30 days)
- Tarawih tracker
- Last 10 nights special reminders (Laylat al-Qadr search)

---

## Technical Debt & Code Quality

### 9. Split Into Multiple Files
The single HTML file is now 2500+ lines. Consider splitting:
- `index.html` — markup only
- `styles.css` — all CSS
- `app.js` — core app logic
- `calendar-builder.js` — ICS generation
- `data.js` — OCC, PREP, DAILY_INSPO, WMD arrays

### 10. Testing
- Unit tests for ICS generation (verify UIDs, dates, RRULE format)
- Visual regression tests for the UI
- Cross-browser testing (Safari, Chrome, Firefox, mobile)

### 11. Accessibility
- Screen reader support (ARIA labels, role attributes)
- Keyboard navigation for all interactive elements
- High contrast mode option

---

## Sync Strategy Summary

| Method | Effort | UX | Auto-sync? |
|--------|--------|-----|------------|
| `.ics` download (current) | Done | Manual re-import | No |
| Deterministic UIDs (current) | Done | Re-import updates | Partial |
| Hosted `.ics` subscription | Medium | Subscribe once | Yes (12-24h) |
| Dynamic subscription URL | Medium-High | Subscribe once, personalized | Yes |
| PWA push notifications | High | Native-like | Yes (instant) |

**Recommended progression:** Current → Hosted subscription → Dynamic URL → PWA

---

*Last updated: March 2026*
