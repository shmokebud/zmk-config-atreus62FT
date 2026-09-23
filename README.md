# zmk-config-atreus62

ZMK firmware configuration for the **Atreus62** keyboard (Falbatech FT / profet23
variant) running on a **nice!nano** controller, with **ZMK Studio** support.

> ⚠️ **Hardware requirement.** ZMK does not support ATmega32U4 (the original
> Pro Micro AVR controller). You **must** replace the Pro Micro with a
> nice!nano v2 (or another nRF52840 board with the Pro Micro footprint, such
> as a Supermini nRF52840 or ProMicro nRF52840 clone). The nice!nano dropwfentmarsietnmairesntmarss in
> as a pin-compatible replacement.

## What's in the box

| Path | Purpose |
|---|---|
| `boards/shields/atreus62/atreus62.dtsi` | Matrix, transform, physical layout |
| `boards/shields/atreus62/atreus62.overlay` | Top-level shield overlay |
| `boards/shields/atreus62/atreus62.keymap` | Default 4-layer keymap |
| `boards/shields/atreus62/atreus62.conf` | Power/battery defaults |
| `boards/shields/atreus62/atreus62.zmk.yml` | Module metadata |
| `boards/shields/atreus62/Kconfig.shield` | Kconfig registration |
| `boards/shields/atreus62/Kconfig.defconfig` | Default values |
| `config/west.yml` | West manifest pointing at upstream ZMK |
| `build.yaml` | What to build in CI (nice!nano v2 + studio) |
| `.github/workflows/build.yml` | GitHub Actions trigger |

## How to use this

1. **Fork or copy this repo** to your own GitHub account.
2. Push to your fork — GitHub Actions will build the firmware automatically.
3. Download the resulting artifact zip from the Actions tab. Inside you'll
   find `atreus62-nice_nano_v2-zmk.uf2`.
4. Put your nice!nano in bootloader mode (double-tap reset) — it shows up as
   a USB mass-storage device. Drag the UF2 file onto it. The board reboots
   into ZMK.
5. Pair with your computer over Bluetooth (the keyboard advertises itself as
   `Atreus62 FT`). Or use USB.
6. Open [ZMK Studio](https://zmk.studio/) in Chrome/Edge, click **Connect**,
   and you can remap every key live without rebuilding firmware.

To unlock the keyboard for Studio edits, press the **adjust-layer key in the
top-left** (position 0). That's bound to `&studio_unlock` in this config.

## Pin mapping (the part you'll want to double-check)

This config inherits the matrix from the upstream profet23/atreus62 QMK
configuration. The Falbatech FT PCB **should** be electrically identical, but
if some keys don't respond, recheck against your actual board.

| Function | Pro Micro pad (silkscreen) | ZMK reference |
|---|---|---|
| Row 0 (top) | D2 | `&pro_micro 2` |
| Row 1 | D3 | `&pro_micro 3` |
| Row 2 | D1 | `&pro_micro 1` |
| Row 3 | D0 | `&pro_micro 0` |
| Row 4 (thumbs) | D4 | `&pro_micro 4` |
| Col 0 (leftmost) | F4 | `&pro_micro 21` |
| Col 1 | F5 | `&pro_micro 20` |
| Col 2 | F6 | `&pro_micro 19` |
| Col 3 | F7 | `&pro_micro 18` |
| Col 4 | B1 | `&pro_micro 15` |
| Col 5 | B3 | `&pro_micro 14` |
| Col 6 (inner thumbs only) | B2 | `&pro_micro 16` |
| Col 7 | B6 | `&pro_micro 10` |
| Col 8 | B5 | `&pro_micro 9` |
| Col 9 | B4 | `&pro_micro 8` |
| Col 10 | E6 | `&pro_micro 7` |
| Col 11 | D7 | `&pro_micro 6` |
| Col 12 (rightmost) | C6 | `&pro_micro 5` |

> 🛠 **Heads up.** `&pro_micro N` references the pad position (the Arduino
> pin number printed on the Pro Micro silkscreen), not any specific nRF GPIO,
> so the same config works across nice!nano v1, v2, and Pro-Micro-compatible
> clones.

## Matrix layout

This shield assumes the **profet23 wiring**: 5 row pins, 13 column pins, with
column 6 used only by the two inner-thumb keys on row 4. The top four rows
each have 12 keys (cols 0–5 and 7–12), and row 4 has 14 keys (all columns).

The "FT" Falbatech version is sold as a pre-soldered build of the same PCB —
the maintainer copies the upstream profet23 design. If you have a different
revision (e.g. a hand-wired build, or a Falbatech revision that rearranges
the matrix), edit `atreus62.dtsi` accordingly.

## Customizing the keymap

You have two options:

- **Live editing via ZMK Studio** — recommended. No rebuild needed. Changes
  persist in the keyboard's settings storage.
- **Static keymap edits** — modify `boards/shields/atreus62/atreus62.keymap`
  and push. CI rebuilds. ⚠️ Once you start using Studio, any further static
  keymap edits will be ignored unless you do "Restore Stock Settings" in
  Studio.

## Troubleshooting

- **Nothing types / wrong keys** → Likely a matrix mismatch. Build the
  `tester_pro_micro` shield from upstream ZMK and short pads to ground one by
  one to identify which `&pro_micro N` corresponds to which physical column
  on your specific board.
- **Studio can't connect** → Make sure you flashed with the
  `studio-rpc-usb-uart` snippet (it's enabled in `build.yaml`) and that you
  pressed `&studio_unlock` after every reboot.
- **Battery drain** → The default `CONFIG_ZMK_IDLE_SLEEP_TIMEOUT` is 15
  minutes. Tune `atreus62.conf` to taste.

## Credits

- [profet23/atreus62](https://github.com/profet23/atreus62) — original PCB design
- [Falbatech](https://falbatech.click) — the FT pre-soldered variant
- [ZMK Firmware](https://zmk.dev) — the firmware framework
- [nice!nano](https://nicekeyboards.com/nice-nano) — the controller

## License

MIT, matching ZMK's license.
