# Home Assistant Configuration Library

This repository contains reusable artifacts from my Home Assistant environment, including Home Assistant YAML snippets and ESPHome device configurations.

## Contents

- Home Assistant YAML (automations, scripts, templates, dashboards, packages, blueprints)
- ESPHome YAML (device configs, shared components, display configs)
- Supporting documentation and assets (images/diagrams where relevant)

## Recommended Repository Structure

```
.
├─ home-assistant/
│  ├─ automations/
│  ├─ scripts/
│  ├─ templates/
│  ├─ dashboards/
│  ├─ packages/
│  ├─ integrations/
│  └─ blueprints/
├─ esphome/
│  ├─ devices/
│  ├─ common/
│  └─ secrets.example.yaml
├─ assets/
│  ├─ images/
│  └─ diagrams/
├─ docs/
└─ README.md
```

### What goes where

- `home-assistant/`  
  Home Assistant YAML content that is reusable or illustrative. Prefer smaller snippets or `packages/` over committing an entire raw `configuration.yaml`.

- `home-assistant/packages/`  
  Self-contained “feature bundles” that can include sensors, automations, scripts, helpers, and templates in one file.

- `esphome/devices/`  
  One YAML per ESPHome node/device. File name should match (or clearly map to) the ESPHome `name:`.

- `esphome/common/`  
  Reusable blocks (substitutions, shared sensors, shared lambdas, shared display/page code).

- `assets/`  
  Images used in dashboards or ESPHome displays (e.g., background images, icons, screenshots).

- `docs/`  
  Notes, design decisions, wiring diagrams, hardware bills of materials, and troubleshooting.

## Conventions

### Naming

- Home Assistant:
  - `home-assistant/automations/<domain>-<purpose>.yaml`
  - `home-assistant/scripts/<purpose>.yaml`
  - `home-assistant/templates/<purpose>.yaml`
  - `home-assistant/packages/<feature>.yaml`

- ESPHome:
  - `esphome/devices/<device_name>.yaml`

### Use `!secret` everywhere possible

Home Assistant example:

```yaml
mqtt:
  broker: !secret mqtt_host
  username: !secret mqtt_user
  password: !secret mqtt_pass
```

ESPHome example:

```yaml
wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
```

## Safety / Redaction Policy (Non-Negotiable)

This repo must never contain:

- API keys, tokens, client secrets
- Wi-Fi passwords
- Private URLs that grant access (webhooks, signed links)
- GPS coordinates / precise address information
- Personally identifying details embedded in configs

### Required secret file approach

- `secrets.example.yaml` (committed)
- `secrets.yaml` (NOT committed)

Create `esphome/secrets.example.yaml` similar to:

```yaml
# Home Assistant (example placeholders)
mqtt_host: 192.168.1.10
mqtt_user: example
mqtt_pass: example

# ESPHome (example placeholders)
wifi_ssid: example
wifi_password: example
ota_password: example
api_encryption_key: "BASE64_KEY_HERE"
```

## Home Assistant Usage

### Option A: Directory includes (snippets)

If you organize by folders, you can merge includes from directories.

Example `configuration.yaml` include pattern:

```yaml
automation: !include_dir_merge_list home-assistant/automations/
script: !include_dir_merge_named home-assistant/scripts/
template: !include_dir_merge_list home-assistant/templates/
```

Notes:
- Use `merge_list` where files contain YAML lists (e.g., automations).
- Use `merge_named` where files contain named maps (e.g., scripts).

### Option B: Packages (recommended)

Packages are ideal for modular “features” and portability.

Example `configuration.yaml`:

```yaml
homeassistant:
  packages: !include_dir_named home-assistant/packages/
```

## ESPHome Usage

1. Copy device YAMLs into your ESPHome config folder (or keep this repo as your ESPHome config root).
2. Create `secrets.yaml` locally using `secrets.example.yaml` as your template.
3. Compile and install via ESPHome Dashboard or CLI.

## Validation Checklist (Before Commit)

- [ ] No secrets committed (keys/tokens/passwords)
- [ ] All sensitive values use `!secret`
- [ ] Any URLs are non-sensitive (no webhook endpoints or signed links)
- [ ] Files are named consistently and stored in the correct folder
- [ ] If adding a new secret key, update `secrets.example.yaml`
- [ ] Restart/reload in Home Assistant after changes (where applicable)

## Documentation Practices

When adding new items, include minimal notes that make future reuse possible:

- Purpose / what problem it solves
- Dependencies (integrations required, entities expected)
- Any hardware assumptions (for ESPHome)
- Known quirks / troubleshooting steps

Suggested pattern (top of file as comments):

```yaml
# Purpose: Track daily water usage from a pulse meter.
# Dependencies: ESPHome pulse_counter, Home Assistant API.
# Notes: Resets daily at midnight using SNTP time.
```

## Troubleshooting

### Home Assistant

- YAML include errors: verify include type (`merge_list` vs `merge_named`) and file structure.
- Entities not appearing: confirm file placement, includes, and restart Home Assistant.
- Template issues: check Developer Tools → Template.

### ESPHome

- Compile errors: validate indentation and component compatibility.
- Device not connecting: check Wi-Fi secrets and API encryption key.
- Display issues (LVGL/TFT): confirm correct driver, pin mapping, and font/assets availability.

## Roadmap / Backlog

- [ ] Standardize ESPHome devices to use shared `esphome/common/` blocks
- [ ] Add diagrams for network + MQTT + device topology
- [ ] Add “golden patterns” docs:
  - deadbands / noise filtering
  - daily reset counters
  - uptime/health monitoring
  - alerting conventions (mobile, voice, persistent notifications)

## License

Private/personal configuration repository. If this repo becomes public, add an explicit license (MIT/Apache-2.0/etc.) and re-review redaction carefully.
