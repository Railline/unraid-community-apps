# Railline Unraid Community Apps

Unraid Community Applications repository for templates and plugins maintained by Railline.

## Included Apps

### CrowdSec Unraid Bouncer

Unraid plugin that consumes CrowdSec Local API decisions and applies them to host and Docker-published traffic through iptables, with ipset support for large global decision sets and WebUI health notifications.

- CA wrapper: `plugins/crowdsec-unraid-bouncer.xml`
- Plugin manifest: `plugins/crowdsec-unraid-bouncer.plg`
- Details: `docs/crowdsec-unraid-bouncer.md`

### NPMplus

Unraid Docker template for NPMplus, an improved fork of Nginx Proxy Manager.

- Docker template: `templates/NPMplus.xml`
- Upstream project: <https://github.com/ZoeyVid/NPMplus>

## Community Applications

Submit this repository URL to Community Applications:

```text
https://github.com/Railline/unraid-community-apps
```

This repository follows the current CA layout:

- `ca_profile.xml` in the repository root
- OSI-approved `LICENSE`
- Docker templates under `templates/`
- Plugin wrappers/manifests under `plugins/`
- Public icons under `assets/`
- One repository for all shared Unraid apps

## Support

Use GitHub issues in this repository for template/plugin packaging issues:

<https://github.com/Railline/unraid-community-apps/issues>

For application-specific NPMplus behavior, use the upstream NPMplus project:

<https://github.com/ZoeyVid/NPMplus>
