# CLAUDE.md

## Project Overview

SnowLine is an application for mountain athletes that identifies areas of snow and estimates snow quality at the time of travel. It uses Sentinel-2 satellite imagery processed through Google Earth Engine to detect snow cover, track data freshness, and (eventually) estimate snow conditions along routes.

**Current status:** Stage 1 (Data Pipeline) nearly complete — route/snow intersection is the remaining task. See ROADMAP.md for the full 7-stage plan.

## Repository Structure

```
SnowLine/
├── notebooks/
│   └── sentinel2_snow_test.ipynb   # Primary data pipeline notebook
├── sentinel2_snow_test.ipynb       # Root-level copy of notebook
├── snowline-app.html               # Static UI/interaction mockup (not connected to pipeline)
├── ROADMAP.md                      # 7-stage development roadmap
├── DECISIONS.md                    # Technical decision log
├── README.md                       # Brief project description
├── .gitignore                      # Ignores credentials, .env, caches, .vscode
└── CLAUDE.md                       # This file
```

## Tech Stack

- **Language:** Python
- **Package manager:** Conda (no `environment.yml` checked in yet)
- **Notebooks:** VS Code (previously Google Colab)
- **Satellite data:** Google Earth Engine API (`earthengine-api`)
- **Mapping:** `geemap` for interactive map visualization
- **GEE project:** `snowline-491111`
- **Frontend mockup:** Vanilla HTML/CSS/JavaScript (no frameworks, no build tools)
- **Key Python dependencies:** `earthengine-api`, `geemap`

## Data Pipeline Architecture

The notebook implements this processing chain:

1. **Source:** Sentinel-2 Surface Reflectance Harmonized (`COPERNICUS/S2_SR_HARMONIZED`)
2. **AOI:** Tantalus Range bounding box (`[-123.15, 49.85, -122.85, 50.05]`)
3. **Window:** 60-day rolling window from current date
4. **Per-image processing** (mapped over collection):
   - **Cloud mask:** SCL bands 3 (cloud shadow), 8 (cloud medium), 9 (cloud high), 10 (thin cirrus)
   - **Water mask:** SCL band 6 (kept separate from cloud mask for extensibility)
   - **Combined valid mask:** cloud mask AND water mask
   - **NDSI:** `normalizedDifference(['B3', 'B11'])` — threshold at 0.4
   - **Timestamp:** `system:time_start` metadata added as band
5. **Compositing:** `qualityMosaic('timestamp')` — most recent valid pixel wins
6. **Output bands:** `snow` (binary) + `timestamp` (for pixel age/staleness)

## Key Technical Decisions

Always consult `DECISIONS.md` before making architectural choices. Key decisions:

- **NDSI threshold:** 0.4 (standard for snow detection)
- **No image-level cloud filtering:** Do not filter by `CLOUDY_PIXEL_PERCENTAGE`. Per-pixel SCL masking handles clouds at the right granularity. A 99% cloudy image still contributes valid pixels.
- **Separate water and cloud masks:** Keep them as independent masks for future extensibility (e.g., snow-covered lake detection)
- **Staleness bands (placeholder):** <3 days = fresh, 3-7 days = moderate, >7 days = stale
- **SCL snow class not used as ground truth:** ESA's classifier degrades in alpine terrain. Validation is via visual inspection against true-colour imagery.
- **Future water mask upgrade:** Replace SCL water mask with JRC Global Surface Water for snow-covered lake preservation (backlog)

## Frontend Mockup

`snowline-app.html` is a **static design mockup** with hardcoded demo data. It is not connected to the Earth Engine pipeline.

- Two views: `#landing-view` (marketing/features) and `#app-view` (route planner demo)
- Vanilla JS with direct DOM manipulation (no framework)
- CSS custom properties for theming (e.g., `--ice`, `--glacier`, `--deep`)
- Fonts: DM Sans, DM Mono, Bebas Neue (Google Fonts)
- Interactive elements: departure time slider, date slider, crossing report cards

## Development Workflow

- **Notebooks:** Developed in VS Code with Jupyter extension
- **HTML mockup:** Edited standalone, opened directly in browser
- **No CI/CD, tests, linting, or build step**
- **No `requirements.txt` or `environment.yml`** — dependencies managed via Conda but not pinned in repo yet
- **Commit style:** Informal, imperative mood ("update...", "add...")

## Credentials & Security

The following are gitignored and must never be committed:

- `credentials/` — Earth Engine service account keys
- `private-key.json` — Earth Engine private key
- `.env` / `*.env` — Environment variables

Earth Engine authentication: `ee.Authenticate()` + `ee.Initialize(project='snowline-491111')`

## Current Roadmap Status

- **Stage 1 (Data Pipeline):** Nearly complete. Route/snow intersection is remaining.
- **MVP = Stages 1-3:** Real snow data + interactive map + route intersection with crossing reports.
- **Stage 4 (Condition Estimation):** Most differentiated feature — prioritize after MVP.
- See `ROADMAP.md` for full details on all 7 stages.

## Conventions for AI Assistants

1. **Read DECISIONS.md** before making any architectural or algorithmic choices — it documents why things are done a certain way.
2. **Read ROADMAP.md** to understand what's planned, what's in scope, and what's deferred.
3. **Keep water and cloud masks separate** — this is a documented decision for extensibility.
4. **Do not add image-level cloud percentage filtering** — per-pixel SCL masking is the correct approach.
5. **Earth Engine code should use functional patterns** — `collection.map(process_fn)` style, not imperative loops.
6. **The HTML mockup is for design exploration only** — do not attempt to wire it to real data until Stage 2.
7. **No frontend frameworks or build tools yet** — React scaffold is planned for Stage 2. Do not introduce frameworks prematurely.
8. **Credentials are sensitive** — never write Earth Engine keys, `.env` files, or service account JSON to tracked files.
9. **Notebook outputs may be large** — the root-level `sentinel2_snow_test.ipynb` is ~1.1MB due to embedded map widgets.
