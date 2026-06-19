# nerdygriffin.apcupsd

Install and configure [apcupsd](http://www.apcupsd.org/) for APC UPS
monitoring and graceful shutdown on power failure.

Supports the common topologies via the device-connection variables:

- **USB** — apcupsd talks to a locally attached UPS (the defaults).
- **Network management card (PCNET/SNMP)** — apcupsd polls an APC Network
  Management Card directly over the LAN. Every host can run its own client;
  no master is needed.
- **NIS netslave** — a host takes its status from another apcupsd instance
  acting as a `sharemaster`.

## Requirements

None beyond the target distribution's `apcupsd` package (Debian/Ubuntu and
EL families are supported).

## Role Variables

See [`defaults/main.yml`](defaults/main.yml) for the full list and inline
documentation. The variables map 1:1 onto `apcupsd.conf` directives. The most
commonly overridden:

| Variable | Default | Maps to | Notes |
| --- | --- | --- | --- |
| `apcupsd_upsname` | `ups` | `UPSNAME` | ≤ 8 chars, label only |
| `apcupsd_upscable` | `usb` | `UPSCABLE` | `usb`, `ether`, `smart`, … |
| `apcupsd_upstype` | `usb` | `UPSTYPE` | driver: `usb`, `pcnet`, `net`, … |
| `apcupsd_device` | `""` | `DEVICE` | blank for USB; `host:device:<key>` for pcnet; `master:3551` for a netslave |
| `apcupsd_batterylevel` | `5` | `BATTERYLEVEL` | shutdown at this charge % |
| `apcupsd_minutes` | `3` | `MINUTES` | shutdown at this remaining runtime |
| `apcupsd_netserver` | `true` | `NETSERVER` | serve status via NIS |
| `apcupsd_nisip` | `127.0.0.1` | `NISIP` | bind address for NIS |

## Example Playbook

Network management card (PCNET) client — every host polls the card directly:

```yaml
- hosts: ups_clients
  become: true
  roles:
    - role: nerdygriffin.apcupsd
      vars:
        apcupsd_upsname: SMT1500
        apcupsd_upscable: ether
        apcupsd_upstype: pcnet
        apcupsd_device: "10.9.20.5:device:{{ vault_apcupsd_nmc_passphrase }}"
```

## License

GPL-3.0-or-later

## Author Information

Created by [NerdyGriffin](https://github.com/NerdyGriffin).
