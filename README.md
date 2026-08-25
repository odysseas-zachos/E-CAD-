ROM address offsets.
* `Counters.v`: Dual horizontal (0–800) and vertical (0–449) pixel coordinate tracking engine.
* `sync_signal_generator.v`: Generates `HSYNC` (active-low), `VSYNC` (active-high), and `video_on` blanking flags.
* `characterRom.v`: Hardware Lookup Table (LUT) containing 16x8 bitmap graphics for `F`, `Q`, `H`, `X`.
* `charRegister.v`: Screen coordinate target window evaluator (217–233 V, 396–404 H) driving RGB pins (`0x1FF` / `0x000`).
* `vga_controller.v`: Top-level display sub-controller orchestrating counters, timing generators, and dividers.
* `parameters.v`: Global parameter configuration file defining display bounds and systemic hardware constants.

---

## 📺 VGA Specification & Hardware Pinout

### Video Timing (640x400 @ 70Hz)
* **Pixel Clock:** 25 MHz
* **Horizontal:** Visible = 640 px | Front Porch = 16 px | HSYNC Pulse = 96 px | Back Porch = 48 px (Total = 800 px)
* **Vertical:** Visible = 400 lines | Front Porch = 12 lines | VSYNC Pulse = 2 lines | Back Porch = 35 lines (Total = 449 lines)

### FPGA Pin Assignments

| Signal | FPGA Pin | Description |
| :--- | :---: | :--- |
| `PWM` | `M3` | Servo Motor Control Signal |
| `VGA-RED[2:0]` | `B1`, `D6`, `C8` | 3-bit Red Video Channel |
| `VGA-GREEN[2:0]` | `C3`, `A5`, `A8` | 3-bit Green Video Channel |
| `VGA-BLUE[2:0]` | `D5`, `E7`, `C9` | 3-bit Blue Video Channel |
| `VGA-HSYNC#` | `B7` | Horizontal Sync Signal (Active Low) |
| `VGA-VSYNC#` | `D8` | Vertical Sync Signal (Active High) |

---

## 📹 Hardware Demo

▶️ **[Watch Physical Board & Servo Operation on YouTube](https://youtu.be/P8hzgiHTz4k)**
