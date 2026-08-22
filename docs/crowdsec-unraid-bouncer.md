# CrowdSec Unraid Bouncer

<p align="center">
  <img src="../assets/crowdsec-unraid-bouncer.png" alt="CrowdSec Unraid Bouncer" width="128">
</p>

CrowdSec Unraid Bouncer reads CrowdSec ban decisions and enforces them on the Unraid host firewall and Docker-published ports.

Docker-published traffic normally traverses `FORWARD` and `DOCKER-USER`, not only `INPUT`. The plugin therefore installs one managed `CROWDSEC` chain and places a jump at the beginning of both `INPUT` and `DOCKER-USER` by default.

## Scope

The plugin has one job: apply IPv4 `ban` decisions from the CrowdSec Local API to Unraid's firewall. It does not collect firewall-drop telemetry or manage CrowdSec scenarios and parsers.

It provides:

- full and incremental synchronization through `/v1/decisions/stream`;
- independent tracking of decision IDs, so removing one decision cannot remove another active ban for the same address;
- direct `iptables` rules for small local lists;
- an `ipset` backend for large CAPI/list decision sets;
- safe backend switching without temporarily dropping existing protection;
- periodic full synchronization to recover from missed deltas;
- Unraid and optional Telegram health notifications;
- automatic creation of a dedicated CrowdSec bouncer API key when none is configured.

Only IPv4 and IPv4 ranges are enforced. IPv6 decisions are ignored until an IPv6 firewall backend is implemented.

## Requirements

- Unraid 6.9 or newer;
- Docker enabled;
- a running CrowdSec container with `cscli`, named `crowdsec` by default;
- the CrowdSec Local API exposed to the Unraid host, at `http://127.0.0.1:8082` by default.

The plugin installs the required `ipset-7.15-x86_64-1.txz` package automatically when Unraid does not already provide `ipset`. Its SHA-256 checksum is verified before installation.

## Installation

Install the plugin from Community Applications, paste this URL into **Plugins > Install Plugin**, or run:

```bash
plugin install https://raw.githubusercontent.com/Railline/unraid-community-apps/main/plugins/crowdsec-unraid-bouncer.plg
```

The settings page is available at **Settings > Network Services > CrowdSec Unraid Bouncer**.

## Protection modes

- **Essential shield** includes local CrowdSec decisions and manual `cscli` decisions. With `auto`, it uses direct `iptables` rules.
- **Global shield** also includes CAPI and list decisions. With `auto`, it uses `ipset`, which is designed for large lists.
- **Custom origins** uses the comma-separated origins entered in `STREAM_ORIGINS`.

The `auto` backend selects direct rules for Essential/Custom and `ipset` for Global. The `MAX_DIRECT_RULES` limit prevents an accidentally large list from generating thousands of individual rules.

## Configuration

The persistent configuration is stored at:

```text
/boot/config/plugins/crowdsec-unraid-bouncer/config.cfg
```

Leave the API key field empty in the WebUI to retain its existing value. If no key exists yet, the service creates and stores one during its first connection.

`SKIP_PRIVATE_IPV4=yes` ignores private, loopback, link-local, shared, multicast, reserved, and overlapping IPv4 ranges to reduce accidental lockout risk.

The watchdog checks that:

- the daemon is running and the Local API is reachable;
- the managed firewall chain and first-position jumps exist without duplicates;
- the firewall backend contains the same unique addresses as the internal decision state;
- the last successful synchronization is recent enough.

Alerts can be delivered through Unraid notifications and, optionally, the Telegram endpoint already configured in CrowdSec's `http.yaml`.

## Service commands

```bash
/etc/rc.d/rc.crowdsec-unraid-bouncer start
/etc/rc.d/rc.crowdsec-unraid-bouncer stop
/etc/rc.d/rc.crowdsec-unraid-bouncer restart
/etc/rc.d/rc.crowdsec-unraid-bouncer status
crowdsec-unraid-bouncer test
crowdsec-unraid-bouncer health
crowdsec-unraid-bouncer sync
```

Stopping or disabling the service removes its managed firewall jumps, chain, set, and runtime decision state. Starting it again performs a full synchronization. Persistent configuration and the Local API key remain intact.

## Verification and troubleshooting

Use the WebUI's **Test LAPI**, **Health check**, and **Sync now** actions first. From a terminal:

```bash
crowdsec-unraid-bouncer status
iptables -vnxL INPUT --line-numbers
iptables -vnxL DOCKER-USER --line-numbers
iptables -vnxL CROWDSEC --line-numbers
ipset list crowdsec4 | sed -n '1,20p'
tail -80 /var/log/crowdsec-unraid-bouncer.log
```

The `CROWDSEC` jump should be the first rule in each configured protected chain.

## Removal

Remove the plugin through Unraid Plugin Manager or run:

```bash
plugin remove crowdsec-unraid-bouncer.plg
```

Removal stops the daemon, removes managed runtime/firewall state and the watchdog entry, and deliberately keeps `config.cfg` so a reinstall can restore the existing settings.

## Community Applications and support

- Plugin wrapper: <https://raw.githubusercontent.com/Railline/unraid-community-apps/main/plugins/crowdsec-unraid-bouncer.xml>
- Plugin manifest: <https://raw.githubusercontent.com/Railline/unraid-community-apps/main/plugins/crowdsec-unraid-bouncer.plg>
- Support: <https://github.com/Railline/unraid-community-apps/issues>
