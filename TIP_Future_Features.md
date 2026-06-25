# Turf Intelligence Platform — Future Features Backlog
*Last updated: v13 · Compiled from design sessions and field trials*

---

## 🔴 High Priority — Next Build Candidates

### Meter Type & OCR
- **[ ] Analog needle detection algorithm** — on-device computer vision to read Turf-Tec and similar analog arc-scale meters without requiring Claude API. Approach: HSV color filtering to isolate needle, Hough line transform for angle, map angle to 0–100% scale. Requires per-meter-model calibration values.
- **[ ] Custom TF.js model for specific meter hardware** — ~50 training photos of the actual meter used, trained as a JavaScript digit classifier. Expected accuracy 97–99%. Works offline, no API key. Half-day effort once training photos are provided.
- **[ ] Meter model database / dropdown** — named presets for common meters (Turf-Tec, Spectrum TDR 350, Field Scout TDR 300, Aquaterr T-300, Lincoln, Reotemp) with pre-configured display type and calibration settings. User picks their meter from a list at setup rather than selecting abstract "digital/analog."

### Survey & Capture
- **[ ] Green completion minimum alert** — configurable minimum point count per green (e.g. 40 for Full Survey). When user taps Next Green below that count, show a warning: "You have 23 of 40 recommended points — continue anyway?" Prevents incomplete surveys that only appear sparse later in the heatmap.
- **[ ] Voice value entry** — Web Speech API in Safari on iOS recognizes spoken numbers. After capture, speak "forty-three" → value field auto-fills. Eliminates thumb-typing with wet hands. Falls back gracefully on older devices.
- **[ ] Persistent GPS accuracy warning** — if accuracy drops below ±15m (cell tower positioning instead of true GPS), capture button turns amber and a small badge shows "⚠ GPS weak." Currently only the accuracy number is displayed without a clear threshold warning.

### Intelligence
- **[ ] Cumulative ET between surveys** — currently shows instantaneous ET rate from current weather. Should sum daily ET estimates for each day since last survey date to give "total moisture lost since Monday" rather than a per-hour rate. More actionable for irrigation scheduling.
- **[ ] Irrigation event markers on trend chart** — logged irrigation events should appear as vertical dashed blue lines on the moisture trend chart with a 💧 icon. Makes the cause-and-effect of watering decisions immediately visible in the data.
- **[ ] 7-day weather forecast integration** — Open-Meteo already provides free forecast data. Pull next 7 days of temp/humidity/wind to project ET and predict which greens will hit critical moisture by day. "Green 7 projected to drop below threshold by Thursday" early-warning system.

---

## 🟡 Medium Priority — Phase 2 Features

### Platform & Users
- **[ ] Login system per course with user roster** — each course gets a PIN or password. Superintendent creates a roster of staff names. Before each survey, select from a dropdown rather than typing. All captures are tagged with the authenticated user. Foundation for multi-device sync.
- **[ ] Multi-user real-time data merge** — two staff members survey different greens simultaneously and data merges into one session. Requires cloud backend (Supabase recommended — free tier is sufficient for a single course).
- **[ ] Cloud sync backend (Supabase)** — data survives device loss, accessible on multiple devices, enables real-time multi-user. Store: sessions, readings, photos (compressed), irrigation events, boundaries, sprinkler heads. Personal data only, no cross-course sharing without explicit opt-in.
- **[ ] Superintendent web dashboard** — browser-based overview (not mobile) showing all greens in a table, today's alerts, irrigation log, ET summary, and export controls. Designed for the office/shop screen rather than in-field use.

### Agronomic Features
- **[ ] Seasonal grass type switching** — set a "summer grass" and "winter grass" profile per course with a switch date for each. App prompts with one-tap confirmation when the calendar date crosses the seasonal boundary. Thresholds and alert values flip automatically. *(Tabled from v10 — schedule for Phase 2)*
- **[ ] Soil temperature field** — second probe value input per capture point for dual-reading devices like the Spectrum TDR 350. Stored alongside moisture. Visible in heatmap as a toggle layer. Added to CSV export.
- **[ ] Aeration event tagging** — log date of aeration per green. This resets the moisture baseline (post-aeration readings are not comparable to pre-aeration). App marks these events on the trend chart and excludes the pre/post boundary from drop-alert calculations for 7 days.
- **[ ] Custom moisture target ranges per green** — some greens are managed wetter or drier by design (slope drainage, grass variety, tournament prep). Let superintendent set a per-green target band that overrides course thresholds for alerts and color coding.
- **[ ] Pre-irrigation vs. post-irrigation session flag** — tag each survey session as "pre-irrigation" or "post-irrigation." Predictions and trend comparisons are only meaningful between same-type sessions. Morning surveys (pre) and evening surveys (post) tell completely different stories.

### Visualization
- **[ ] Share heatmap as image** — canvas.toBlob() + Web Share API generates a PNG of the current green's heatmap and shares it directly to Messages, WhatsApp, or email. Send crew exactly where to hand-water without them needing app access.
- **[ ] Swipe between greens on map** — left/right swipe gesture on the map tab cycles to next/previous green's heatmap. Eliminates navigation to session bar for post-survey review.
- **[ ] PDF report generator** — one-tap export of a professional PDF: course name, date, all greens avg/min/max table, heatmap images per green, weather conditions, irrigation events since last report, LDS alerts, surveyor name. Suitable for club management or USGA compliance reporting.

---

## 🟢 Lower Priority — Phase 3 / Commercial Features

### AI & Machine Learning
- **[ ] Dry zone prediction model** — after 60–90 days of data, ML model predicts which zones will be below threshold 24–48 hours ahead based on weather forecast + historical drying patterns per green. Moves from reactive to preventive irrigation.
- **[ ] Visual stress detection** — photo AI (Claude Vision) analyzes the meter display photo for turf discoloration, wilt, or stress patterns visible in the background. Logs observations alongside moisture readings. Catches stress before it registers on the probe.
- **[ ] NDVI drone calibration layer** — ground-truth GPS readings serve as calibration points for drone NDVI imagery. Upload a drone NDVI image, align it to the satellite base layer, and the app cross-references moisture readings with vegetation index values. Research-grade diagnostics.

### Business / Product
- **[ ] Multi-course labeling and organization** — course selector at the top of the app, each course maintains its own greens list, settings, grass type, and history. Required for resort properties (Streamsong Red/Blue/Black) and management companies (Troon, ClubCorp). *(Tabled from v9 — single course first)*
- **[ ] Anonymous benchmarking database** — opt-in aggregation of moisture data across courses. Superintendent sees "your Green 7 avg is 28% — the median for TifEagle greens in your climate zone this week is 41%." Requires cloud backend and privacy policy.
- **[ ] Water savings calculator** — estimates gallons and cost saved vs. a baseline over-irrigation assumption. Running total on the dashboard: "Est. 84,000 gallons saved · $1,240 since May 1st." Strongest ROI talking point for club management and sales pilots.
- **[ ] Native iOS app (Capacitor.js)** — wraps TIP in a native iOS container, enabling Apple Vision Framework for on-device OCR (free, fast, accurate, no API key), push notifications for dry spot alerts, and App Store distribution. Required for enterprise sales at scale.
- **[ ] GCSAA / USGA reporting export** — structured data export format compatible with USGA Deacon or GCSAA research data submission standards. Positions TIP as a complementary tool to institutional programs rather than a competitor.

---

## 📝 Technical Debt / Infrastructure

- **[ ] Service worker update flow** — currently shows a toast when a new version is available. Should offer a one-tap "Update Now" that clears the old cache and reloads.
- **[ ] Photo storage optimization** — photos are stored as base64 in localStorage which has a 5–10MB browser limit. Large surveys with many photos will eventually hit this ceiling. Solution: IndexedDB for photo storage (much larger capacity), with localStorage for metadata only.
- **[ ] Data migration between storage versions** — each version uses a new localStorage key. Historical data from v1–v12 is orphaned. A one-time migration utility on version upgrade would preserve all previous readings.
- **[ ] Offline map tiles** — the satellite imagery requires a network connection. Caching map tiles for the course boundaries in the service worker would make the Map tab fully offline-capable.
- **[ ] Calibration mode** — let users calibrate the app's moisture scale to their specific probe and rootzone by taking a known-saturation reading and a known-dry reading. Adjusts the display without changing the stored raw values.

---

## 🔒 Architecture Decisions Pending

| Decision | Options | Status |
|---|---|---|
| OCR for analog meters | Claude Vision API vs. custom needle algorithm vs. TF.js | *In progress — Claude Vision active, needle algorithm noted* |
| Data storage backend | localStorage only vs. Supabase cloud | *localStorage for now; Supabase when multi-user needed* |
| App distribution | GitHub Pages PWA vs. Capacitor native iOS | *GitHub Pages for now; Capacitor when business scales* |
| Meter model presets | Manual type selection vs. named meter dropdown | *Type selection done; named presets are next step* |
| Login system | PIN-based vs. email auth vs. device-local roster | *Device roster planned as interim before full auth* |

---

*To request prioritization of any item, note it in the next development session.*
