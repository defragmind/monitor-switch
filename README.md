# monitor-switch

Swap monitor inputs between machines via DDC/CI. One keybind, zero OSD fumbling.

Written for a dual-monitor desk setup switching a single Acer VG271U between Linux (DisplayPort) and a Mac Mini (HDMI). The other monitor (Viotek GFT27DB) has broken DDC/CI input support — avoid Viotek.

## Requirements

- `ddcutil` — talks to monitors over I²C
- User in `i2c` group (or sudo)
- Monitor with working DDC/CI VCP 0x60 (Input Source)

## Install

```bash
cp monitor-switch ~/.local/bin/monitor-switch
```

## Usage

```
monitor-switch          # toggle between linux ↔ mac
monitor-switch linux    # switch to DisplayPort
monitor-switch mac      # switch to HDMI-1
monitor-switch status   # show current input
```

## Profiles

Edit the script to match your setup:

- `DISPLAY` — display number from `ddcutil detect`
- `DP` / `HDMI1` / `HDMI2` — VCP 0x60 values (standard: `0x0f`, `0x11`, `0x12`)

## COSMIC Keybind

Add to `~/.config/cosmic/com.system76.CosmicSettings.Shortcuts/v1/custom`:

```ron
(
    modifiers: [Ctrl, Super],
    key: "F12",
): Spawn("/home/byte/.local/bin/monitor-switch toggle"),
```

Hot-reloaded, no restart needed.

## State

Last profile saved to `${XDG_STATE_HOME:-~/.local/state}/monitor-switch-profile`.

## Hardware Notes

- **Acer VG271U** — full DDC/CI, input switching works perfectly
- **Viotek GFT27DB** — brightness/power works over DDC/CI, but VCP 0x60 (Input Source) silently ignores writes. Advertises support in capabilities but firmware doesn't implement it. Do not buy Viotek.

## Roadmap: Full Desk Switching

The end goal is one keybind that swaps *everything* — monitors + keyboard + mouse — between machines, no physical buttons.

### Keyboard/Mouse Sharing (TODO)

**Tested:** `lan-mouse` 0.10.0 — didn't work on COSMIC. GUI empty, no DTLS cert generated, daemon not picking up clients from config. May be a compositor compat issue or a bug in this release.

**Candidates to try next:**
- **input-leap** (Synergy fork) — `extra/input-leap`, more mature, likely more compatible
- **lan-mouse** later releases — actively developed, check back after updates

**Config notes from lan-mouse attempt:**
- Config: `~/.config/lan-mouse/config.toml`
- Mac Mini (zectal) Tailscale IP: `100.81.44.77`
- DTLS fingerprint for zectal: `d4:51:09:ee:6b:4a:2e:cf:3d:ba:ac:88:7c:9b:66:1f:96:08:79:a3:a6:b6:ba:d5:eb:6e:2d:d9:52:84:6e:2c`
- Protocol is UDP 4242, not TCP
- Subnet routing may be needed if not using Tailscale

### Planned Script Integration

Once KB/mouse sharing works, combine into a single `desk-switch toggle` command:
1. `monitor-switch toggle` (ddcutil)
2. Activate/deactivate lan-mouse or input-leap client
3. One COSMIC keybind for the whole thing
