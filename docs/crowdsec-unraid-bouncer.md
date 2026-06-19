# CrowdSec Unraid Bouncer

<p align="center">
  <img src="../assets/crowdsec-unraid-bouncer.png" alt="CrowdSec Unraid Bouncer" width="128">
</p>

CrowdSec Unraid Bouncer is an Unraid plugin that applies CrowdSec ban decisions directly to the Unraid host firewall and Docker-published container traffic.

It uses the CrowdSec Local API bouncer stream, keeps a dedicated `CROWDSEC` iptables chain, protects both `INPUT` and `DOCKER-USER`, and can use `ipset` for large global decision lists.

## Features

- Reads CrowdSec decisions from the Local API stream.
- Protects host services through `INPUT`.
- Protects Docker-published ports through `DOCKER-USER`.
- Supports local decisions, manual `cscli` decisions, CAPI decisions, and blocklists depending on the selected mode.
- Supports direct iptables rules for small local decision sets.
- Supports ipset for large global decision sets.
- Runs as a small Unraid service.
- Starts automatically when installed.
- Includes a WebGUI settings page under `Settings -> Network Services`.
- Includes a health watchdog with Unraid WebUI notifications and optional Telegram alerts through the existing CrowdSec HTTP notification config.

## Requirements

- Unraid 6.9 or newer.
- Docker enabled.
- A running CrowdSec container with `cscli` available.
- CrowdSec Local API reachable from the Unraid host.
- `iptables` on the Unraid host.
- `ipset` when using Global protection mode. The plugin installs the bundled `ipset-7.15-x86_64-1.txz` package automatically when `ipset` is missing.

The plugin validates the bundled ipset package checksum before installing it.

## Install Manually

Open the Unraid WebGUI, then go to:

```text
Plugins -> Install Plugin
```

Install from the raw plugin URL:

```text
https://raw.githubusercontent.com/Railline/unraid-community-apps/main/plugins/crowdsec-unraid-bouncer.plg
```

After installation, open:

```text
Settings -> Network Services -> CrowdSec Unraid Bouncer
```

## Configuration

### Enabled

Starts or disables the bouncer service. Leave this enabled to keep Unraid firewall protection active.

### CrowdSec Container

Docker container name that runs CrowdSec and provides `cscli`. The default is usually correct when the container is named `crowdsec`.

### Local API URL

CrowdSec Local API endpoint reachable from the Unraid host. Default:

```text
http://127.0.0.1:8082
```

This assumes the CrowdSec container publishes container port `8080` to host port `8082`.

### LAPI Stream Timeout

Maximum time allowed for a full decision stream download. Global protection can return tens of thousands of entries, so keep this higher than a normal short HTTP timeout.

Default:

```text
300 seconds
```

### Bouncer API Key

API key registered in CrowdSec for this bouncer. Leave empty only when you want the plugin to create a new key automatically.

### Firewall Chain

Dedicated iptables chain managed by the plugin. Default:

```text
CROWDSEC
```

### Protected Chains

iptables chains that jump into the CrowdSec chain.

Default:

```text
INPUT DOCKER-USER
```

`INPUT` protects host services. `DOCKER-USER` protects Docker-published ports.

### Protection Mode

`Essential shield` imports local CrowdSec decisions and manual `cscli` decisions.

`Global shield` imports local decisions, CAPI decisions, and CrowdSec lists. This mode should use the ipset backend.

`Custom origins` lets advanced users define the exact stream origins manually.

### Firewall Backend

`auto` chooses the safest backend for the selected protection mode.

`iptables direct` creates one DROP rule per decision and is best only for small local decision sets.

`ipset` creates one DROP rule that references a large IP set and is recommended for Global protection.

### Stream Origins

This setting controls which decision origins are imported in Custom mode. Keep `cscli` if you want manual `cscli decisions add` bans to be enforced by the firewall.

### Notifications

The watchdog can alert when protection stops working. It checks that:

- the bouncer daemon is running;
- the CrowdSec Local API is reachable;
- the managed iptables chain exists;
- protected chains jump into the CrowdSec chain;
- the ipset exists and has enough entries when ipset mode is active;
- the last successful sync is recent enough.

Alerts can be sent to:

- Unraid WebUI notifications;
- Telegram, using the existing CrowdSec `http.yaml` notification config.

## CrowdSec Threat Map Drops Export

The optional **CrowdSec Threat Map drops export** setting adds visibility for packets that are actually dropped by the Unraid firewall.

When enabled, the plugin:

- adds rate-limited netfilter `LOG` rules only in front of CrowdSec-managed `DROP` rules;
- reads matching `CROWDSEC_DROP` kernel/syslog lines;
- writes compact JSONL events for CrowdSec Threat Map;
- caps the JSONL file so it cannot grow forever.

Default output:

```bash
/mnt/cache/appdata/crowdsec-threat-map/drops/drops.jsonl
```

The Threat Map container should mount the parent folder read-only:

```text
/mnt/cache/appdata/crowdsec-threat-map/drops -> /crowdsec/drops:ro
```

and use:

```text
DROPS_ENABLED=true
DROPS_LOG_PATH=/crowdsec/drops/drops.jsonl
```

This export is optional and does not change the firewall decision source. It only records packets that were already matched by CrowdSec block rules. Keep the log rate limit conservative to avoid syslog noise during attacks.

## Commands

```bash
/etc/rc.d/rc.crowdsec-unraid-bouncer start
/etc/rc.d/rc.crowdsec-unraid-bouncer stop
/etc/rc.d/rc.crowdsec-unraid-bouncer restart
/etc/rc.d/rc.crowdsec-unraid-bouncer status
/etc/rc.d/rc.crowdsec-unraid-bouncer sync
/etc/rc.d/rc.crowdsec-unraid-bouncer health
```

Direct command:

```bash
crowdsec-unraid-bouncer test
crowdsec-unraid-bouncer status
crowdsec-unraid-bouncer sync
crowdsec-unraid-bouncer health --notify
```

## Verify Firewall Rules

```bash
iptables -vnxL INPUT --line-numbers
iptables -vnxL DOCKER-USER --line-numbers
iptables -vnxL CROWDSEC --line-numbers
ipset list crowdsec4 | sed -n '1,20p'
```

You should see `INPUT` and `DOCKER-USER` jumping to `CROWDSEC`, and the `CROWDSEC` chain should drop sources found in the configured ipset.

## Community Applications

This plugin is shipped from the shared Railline Unraid Community Apps repository:

```text
https://github.com/Railline/unraid-community-apps
```

Plugin wrapper:

```text
https://raw.githubusercontent.com/Railline/unraid-community-apps/main/plugins/crowdsec-unraid-bouncer.xml
```

Plugin manifest:

```text
https://raw.githubusercontent.com/Railline/unraid-community-apps/main/plugins/crowdsec-unraid-bouncer.plg
```

The repository also includes the bundled `ipset` package used when Unraid does not already provide `ipset`.

## Safety Notes

This plugin modifies firewall rules. Review the selected protected chains and notification settings before enabling it on a remote system.

The default configuration avoids importing private IPv4 ranges to reduce lockout risk.

## Support

Open issues at:

```text
https://github.com/Railline/unraid-community-apps/issues
```
