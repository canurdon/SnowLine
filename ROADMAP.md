# SnowLine Development Roadmap

## Stage 1 — Data Pipeline
*Goal: reliable snow data for any given area*

- [x] Sentinel-2 NDSI snow detection
- [x] Cloud masking via SCL
- [x] Water masking
- [x] Per-pixel compositing over 60 day window
- [x] Pixel age tracking
- [ ] Route intersection — detect which segments cross snow
- [ ] Sentinel-1 SAR fallback for persistent cloud
- [ ] Snow change rate validation study (backlog)

## Stage 2 — Core App: Map + Snow Overlay
*Goal: real snow data on a real interactive map*

- [ ] Mapbox/Maplibre base map with topo tiles
- [ ] Export snow composite from Earth Engine as tile layer
- [ ] Render snow overlay on Mapbox map
- [ ] Pixel age staleness display
- [ ] Cloud gap visualisation — explicit unknown zones
- [ ] Basic web app scaffold — React or similar
- [ ] Explorer mode — browse freely, ambient snow layer

## Stage 3 — Route Planning
*Goal: route-specific snow analysis*

- [ ] GPX import
- [ ] Draw-your-own route
- [ ] Route/snow intersection — which segments cross snow
- [ ] Per-crossing report — elevation, length, coverage
- [ ] Elevation profile
- [ ] Transition from Explorer to Route Planner mode
- [ ] Route Planner sidebar with crossing cards

## Stage 4 — Condition Estimation
*Goal: not just where the snow is, but what state it's in*

- [ ] DEM integration — aspect and slope per pixel
- [ ] Solar geometry calculation — when sun hits each slope
- [ ] Temperature forecast integration — Open-Meteo API
- [ ] Freezing level calculation
- [ ] Condition estimation per crossing — firm/marginal/soft
- [ ] Confidence ratings
- [ ] Departure time slider
- [ ] Condition timeline in elevation profile
- [ ] Temperature profile in elevation panel

## Stage 5 — Data Layer Maturity
*Goal: robust, honest, multi-source data*

- [ ] Provider abstraction — SnowTile interface
- [ ] Sentinel-1 SAR provider
- [ ] NISAR provider (when data matures)
- [ ] Snow change rate validation study
- [ ] Logistic regression staleness model
- [ ] Per-pixel confidence combining staleness + cloud
- [ ] Config-driven provider priority

## Stage 6 — Athlete Personalisation
*Goal: arrival time estimates specific to the user*

- [ ] Athlete profile — conversational Claude onboarding
- [ ] Activity logging — manual entry
- [ ] Arrival time estimation per crossing
- [ ] Condition display at estimated arrival time
- [ ] Strava integration (backlog)

## Stage 7 — Polish and Launch Prep
*Goal: something you'd be comfortable showing people*

- [ ] Mobile responsive design
- [ ] Offline capability for planned routes
- [ ] Performance optimisation
- [ ] Error states and edge cases
- [ ] User testing with actual mountain athletes
- [ ] Landing page
- [ ] Analytics

---

## Critical Path

**MVP = Stages 1-3**
Real snow data + real map + route intersection with crossing report.
Useful and unique without any further stages.

**Differentiated product = Stage 4**
Condition estimation is the most differentiated feature.
Prioritise immediately after MVP is stable.

**Current status**
Stage 1 nearly complete — route intersection remaining.