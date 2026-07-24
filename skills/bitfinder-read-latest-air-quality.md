---
name: Read latest Awair air quality
description: Authenticate, find a user's Awair devices, and read the latest air-quality reading for one of them.
api: openapi/bitfinder-awair-openapi.yml
operations:
  - getUserInfo
  - listDevices
  - getLatestAirData
---

# Read latest Awair air quality

Use the Awair Home & OAuth Developer API to fetch the current indoor air quality for a user's device.

## Prerequisites
- A Bearer access token: either a Developer Console token (https://developer.getawair.com/) or an OAuth 2.0 access token from the authorization-code flow (token endpoint https://oauth2.awair.is/v2/token).
- Base URL: `https://developer-apis.awair.is/v1`
- Send `Authorization: Bearer <token>` on every request.

## Steps
1. `getUserInfo` — `GET /users/self`. Confirm the token is valid and note the `tier` (governs per-device daily quotas).
2. `listDevices` — `GET /users/self/devices`. Pick the target device and read its `deviceType` and `deviceId`.
3. `getLatestAirData` — `GET /users/self/devices/{device_type}/{device_id}/air-data/latest`. Add `fahrenheit=true` if you want Fahrenheit temperatures. Read the `data[0]` sample: `score` (Awair Score 0-100) and each `sensors[]` component (temp, humid, co2, voc, pm25).

## Rules
- Read-only, GET-only API — no idempotency key needed (see conventions/bitfinder-conventions.yml).
- Respect per-device daily quotas; a `429` means the quota is exhausted for the day (see rate-limits/bitfinder-rate-limits.yml). Back off rather than retrying immediately.
- Handle `401` (refresh token), `403` (not your device), `404` (bad device_type/device_id) per errors/bitfinder-problem-types.yml.
