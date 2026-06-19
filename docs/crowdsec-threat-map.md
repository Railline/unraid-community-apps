# CrowdSec Threat Map

CrowdSec Threat Map displays local CrowdSec alerts on an interactive world map with a live feed, search, filters and optional dashboard actions.

This Railline fork adds:

- English and French UI (`LANGUAGE=en` or `LANGUAGE=fr`)
- Larger dashboard text for readability
- Optional live firewall drops panel with violet-to-cyan styling
- Optional `/drops` JSON API for firewall/bouncer integrations
- Unraid-friendly template defaults

## Safe Default

For a dashboard-only setup:

- Mount `/crowdsec/data` read-only.
- Leave Docker socket empty.
- Leave `WHITELIST_ENABLED=false`.
- Leave `DROPS_ENABLED=false` unless you provide a drops JSONL file.

The Docker socket is only needed for dashboard unban or dynamic whitelist restart actions.

## Live Drops

The live drops feature is optional. Enable it only when your firewall or bouncer integration writes JSONL events.

Example JSONL line:

```json
{"ts":"2026-06-19T12:30:00Z","ip":"203.0.113.10","country":"FR","packets":12,"bytes":3456,"chain":"DOCKER-USER","rule":"crowdsec-ban"}
```

Required fields:

- `ip`
- `packets` is optional and defaults to `1`
- `bytes`, `country`, `city`, `lat`, `lon`, `chain` and `rule` are optional

If GeoLite2-City.mmdb is available in the CrowdSec data folder, the app can enrich missing country/city/coordinates.

## Links

- App fork: <https://github.com/Railline/crowdsec-threat-map-docker>
- Original project: <https://github.com/kabelsalatundklartext/crowdsec-threat-map-docker>
- Docker image: `ghcr.io/railline/crowdsec-threat-map-docker:latest`
