## Snow Change Rate Validation Study
Status: Backlog — post MVP

Approach: 
Use Sentinel-2 historical archive (2015-2025) to empirically derive snow change rate distributions per elevation band, aspect, month, and temperature. Output: lookup tables that feed pixel-level staleness confidence scores in the compositing engine.

Why deferred: 
Core pipeline needs to work first. Staleness confidence in MVP will use simple day-count bands as a placeholder. The data model is designed to accept empirically derived values when the study is complete — no rework needed to integrate it later.

Data needed: 
Sentinel-2 archive via Earth Engine, temperature reanalysis data (ERA5), DEM for elevation/aspect.

## Snow State Change Probability Model
Status: Backlog — post MVP

Goal: 
Predict the probability that a pixel's snow state has changed since its last cloud-free observation, to generate data-driven staleness confidence scores.

### MVP approach: Joint bins
Create bins combining elevation band, aspect, and month:
- Elevation: <1500m, 1500-2000m, 2000-2500m, 2500m+
- Aspect: North, East/West, South
- Month: Dec-Feb, Mar-Apr, May-Jun, Jul-Nov
= 48 bins total
For each bin calculate empirical change rate from Sentinel-2 historical archive 2015-2025. 
Output: probability of change per day per bin.

Staleness confidence in MVP uses simple placeholder:
- <3 days: fresh
- 3-7 days: moderate
- >7 days: stale

### V2 approach: Logistic regression
Dependent variable: did_change (binary, per consecutive observation pair) 
Independent variables: elevation, aspect, month, temperature, days_elapsed
Add interaction terms: aspect × temperature, elevation × month

Advantages over bins:
- Handles interactions automatically
- No sparse bin problem
- Generalises to any mountain range
- Lightweight at runtime — just store coefficients

Data needed: Sentinel-2 archive via Earth Engine, ERA5 temperature reanalysis, DEM for elevation/aspect.

## Image-level cloud filtering
Decision: Do not filter collection by CLOUDY_PIXEL_PERCENTAGE.

Reason: Image-level filtering discards potentially valid pixels 
in partially clouded images. A 99% cloudy image still contributes 
its 1% of clear pixels to the composite. Per-pixel SCL masking 
handles cloud removal correctly at the right granularity.

Future consideration: At production scale, filter at >99% cloud 
cover purely as a compute optimisation — not a data quality 
decision. Document explicitly if implemented.

## Water masking
Water SCL is used to create a seperate "water_mask"
water and cloud masks are then combined into a single mask for the purpose of determining snow pixels. Keeping the water and cloud masks seperate is a better approach for interprobility and for the addition of other masks at later staged of development. These may be required to ensure that snow cover is calculated accurately without interference from terrain with similar NDSI.  

## Snow covered lake detection
Status: Backlog

Current water mask removes all SCL-classified water pixels 
including potentially snow-covered frozen lakes.

Fix: Replace SCL water mask with JRC Global Surface Water 
conditional mask — only mask water pixels where NDSI is 
below snow threshold. Preserves snow-covered lake pixels 
while still removing open water false positives.

JRC dataset: JRC/GSW1_4/GlobalSurfaceWater
Key band: 'seasonality' — number of months per year water 
is present. Threshold >=10 for permanent water bodies.