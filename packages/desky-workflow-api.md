# desky-workflow REST API

Reference for talking to a device running [desky-workflow.yaml](desky-workflow.yaml) directly over HTTP,
without going through Home Assistant. Useful for building your own UI/automation against the desk.

## Enabling it

This package does not enable `web_server:` itself (no default port/auth choice is safe for everyone).
Add it in your own device YAML:

```yaml
web_server:
  port: 80
  version: 3
  auth:
    username: !secret standing_desk_web_username
    password: !secret standing_desk_web_password
```

`web_server` has no authentication by default, so anyone on your network can control the desk. Set
`auth:` (or otherwise restrict network access) before exposing it.

## URL format

As of ESPHome 2026.1.0, the REST API uses each entity's exact `name:` as the URL path segment
(case-sensitive, spaces and other non-path characters must be percent-encoded, e.g. space -> `%20`).
The older lowercase/underscore `object_id` URL format still works but is deprecated and will be removed
in ESPHome 2026.7.0 — don't rely on it for anything new.

Base URL below is written as `http://<desk-ip>`.

## Read-only entities

| Entity (`name:`) | Domain | GET path | Notes |
|---|---|---|---|
| `Desk Height` | sensor | `/sensor/Desk%20Height` | Current height in cm |
| `is Sitting` | binary_sensor | `/binary_sensor/is%20Sitting` | `ON` = below the sit/stand threshold |

```
curl http://<desk-ip>/sensor/Desk%20Height
curl http://<desk-ip>/binary_sensor/is%20Sitting
```

## Buttons (`POST .../press`)

| Entity (`name:`) | Path | What it does |
|---|---|---|
| `Request Desk Height` | `/button/Request%20Desk%20Height` | Wakes the desk and asks it to report its height |
| `Memory Setting Mode` | `/button/Memory%20Setting%20Mode` | Enters memory-set mode (used internally by the Save Height buttons) |
| `Save Height in Memory 1` | `/button/Save%20Height%20in%20Memory%201` | Saves the current height into desk memory 1 |
| `Save Height in Memory 2` | `/button/Save%20Height%20in%20Memory%202` | Saves the current height into desk memory 2 |
| `Save Height in Memory 3` | `/button/Save%20Height%20in%20Memory%203` | Saves the current height into desk memory 3 |
| `M1` | `/button/M1` | Recalls memory 1 (used as the "sitting" preset by the schedule) |
| `M2` | `/button/M2` | Recalls memory 2 |
| `M3` | `/button/M3` | Recalls memory 3 (used as the "standing" preset by the schedule) |

```
curl -X POST http://<desk-ip>/button/M1
```

## Numbers (`GET` to read, `POST .../set?value=<n>` to write)

| Entity (`name:`) | Path | Unit | Range |
|---|---|---|---|
| `Stand and Sit Height Threshold` | `/number/Stand%20and%20Sit%20Height%20Threshold` | cm | 60-130 |
| `Sitting Time` | `/number/Sitting%20Time` | mins | 0-60 |
| `Standing Time` | `/number/Standing%20Time` | mins | 0-60 |

```
curl -X POST "http://<desk-ip>/number/Sitting%20Time/set?value=30"
```

## Switches (`POST .../turn_on`, `/turn_off`, or `/toggle`)

| Entity (`name:`) | Path | What it does |
|---|---|---|
| `Raise Desk` | `/switch/Raise%20Desk` | Momentary raise (auto turns off after 15s) |
| `Lower Desk` | `/switch/Lower%20Desk` | Momentary lower (auto turns off after 15s) |
| `Work` | `/switch/Work` | Enables/disables the sit/stand schedule |

```
curl -X POST http://<desk-ip>/switch/Work/turn_on
```

## Real-time updates

`GET /events` is a Server-Sent Events stream that pushes `state` events whenever any entity changes,
so a custom UI can subscribe instead of polling. Also sends periodic `ping` (keepalive) and `log`
(debug log) events.

## Keeping this in sync

These paths are derived from the `name:` fields in [desky-workflow.yaml](desky-workflow.yaml). If you
rename an entity there, update the corresponding row here too.
