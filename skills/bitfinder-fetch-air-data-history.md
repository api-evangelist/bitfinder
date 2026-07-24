---
name: Fetch Awair air-data history
description: Pull time-series air-quality history for an Awair device at raw, 5-minute, or 15-minute granularity.
api: openapi/bitfinder-awair-openapi.yml
operations:
  - listDevices
  - getRawAirData
  - get5MinAvgAirData
  - get15MinAvgAirData
---

# Fetch Awair air-data history

Retrieve a window of historical air-quality readings for charting or analysis.

## Prerequisites
- Bearer access token in `Authorization: Bearer <token>`.
- Base URL: `https://developer-apis.awair.is/v1`

## Choose the granularity
- `getRawAirData` — `GET .../air-data/raw` — per-second samples, window max **1 hour**, up to **360** records.
- `get5MinAvgAirData` — `GET .../air-data/5-min-avg` — window max **24 hours**.
- `get15MinAvgAirData` — `GET .../air-data/15-min-avg` — window max **7 days**.

## Steps
1. `listDevices` — `GET /users/self/devices` — resolve `deviceType` and `deviceId`.
2. Call the granularity endpoint for `{device_type}/{device_id}` with query params:
   - `from` / `to` — ISO 8601 window (stay within the endpoint's max span).
   - `limit` — number of records (raw max 360).
   - `desc` — sort direction (default `true`, newest first).
   - `fahrenheit` — optional temperature unit.
3. Iterate `data[]`; each point has `timestamp`, `score`, and `sensors[]`.

## Rules
- Keep the requested `from`/`to` window within the endpoint's maximum span or the request is rejected.
- Watch per-device daily quotas (`429`); prefer coarser granularity for long ranges. See conventions/bitfinder-conventions.yml and rate-limits/bitfinder-rate-limits.yml.
