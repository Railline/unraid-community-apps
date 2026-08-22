# CrowdSec Threat Map

CrowdSec Threat Map displays local CrowdSec alerts on an interactive world map with a live feed, search, filters and optional dashboard actions.

This Railline fork adds:

- English and French UI (`LANGUAGE=en` or `LANGUAGE=fr`)
- Larger dashboard text for readability
- Unraid-friendly template defaults

## Safe Default

For a dashboard-only setup:

- Mount `/crowdsec/data` read-only.
- Leave Docker socket empty.
- Leave `WHITELIST_ENABLED=false`.

The Docker socket is only needed for dashboard unban or dynamic whitelist restart actions.

## Links

- App fork: <https://github.com/Railline/crowdsec-threat-map-docker>
- Original project: <https://github.com/kabelsalatundklartext/crowdsec-threat-map-docker>
- Docker image: `ghcr.io/railline/crowdsec-threat-map-docker:latest`
