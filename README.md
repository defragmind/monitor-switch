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
