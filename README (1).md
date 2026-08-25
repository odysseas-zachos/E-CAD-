# SAPERVAO - FPGA Keyboard, Servo & VGA Controller

[![Verilog](https://img.shields.io/badge/Language-Verilog-blue.svg)](https://en.wikipedia.org/wiki/Verilog)
[![Toolchain](https://img.shields.io/badge/Toolchain-Xilinx%20ISE%20%2F%20Vivado-orange.svg)]()
[![Target FPGA](https://img.shields.io/badge/FPGA-Xilinx%20Spartan--6-green.svg)]()
[![Institution](https://img.shields.io/badge/CEID-University%20of%20Patras-red.svg)](https://www.ceid.upatras.gr/en)

A hardware-level digital system designed in Verilog for real-time PS/2 keyboard processing, PWM servo arm motion control, and multi-resolution VGA display output. Developed for the *Computer Aided System Design (E-CAD)* course at the Department of Computer Engineering & Informatics (CEID), University of Patras (AY 2022–2023, Prof. C. Vergos).

---

## 👥 Authors

* **Odysseas Zachos** (ID: 1072640) — `st1072640@ceid.upatras.gr`
* **Vlasios Panagiotis Panagiotou** (ID: 1067517) — `st1067517@ceid.upatras.gr`
* **Konstantinos Paraskevopoulos** (ID: 1072608) — `st1072608@ceid.upatras.gr`

---

## 📌 Architecture & Design

The system integrates three main digital hardware blocks driven by a 100 MHz clock source:

1. **PS/2 Keyboard Controller:** Decodes Make/Break key codes for state control.
2. **PWM Servo Motor Controller:** Generates 50 Hz PWM signals (5–10% duty cycle, 20ms period) driving Joint 3 of a robotic arm.
3. **VGA Display Engine:** Drives 640x400 @ 70Hz video output, rendering centered 16x8 pixel dynamic status characters via custom ROM lookup tables.

```
                    +-----------------------+
                    |  100 MHz System Clock |
                    +-----------+-----------+
                                |
                                v
                        +---------------+
                        | Clock Divider | (100MHz -> 25MHz Pixel & PWM Clks)
                        +-------+-------+
                                |
       +------------------------+------------------------+
       |                        |                        |
       v                        v                        v
+---------------+      +------------------+      +-------------------+
| PS/2 Keyboard |----->|  Scan2Address &  |----->| PWM Servo Driver  |---> Servo Output (Pin M3)
| Controller    |      |  State Decoder   |      +-------------------+
+---------------+      +--------+---------+
                                |
                                v
                       +------------------+
                       | Character ROM &  |
                       | Character Reg    |
                       +--------+---------+
                                |
                                v
                       +------------------+
                       |  VGA Controller  |---> HSYNC, VSYNC, RGB (640x400 @ 70Hz)
                       +------------------+
```

---

## ⚙️ Control & Modes of Operation

Input signals map PS/2 key scan codes to exact servo motor angles and display state outputs:

| Key | Scan Code | Mode | Robotic Arm Position | PWM Duty Cycle | Pulse Width |
| :---: | :---: | :---: | :--- | :---: | :---: |
| **F** | `2B` | **F**olded | Fully Folded (0°) | 5.00% | 1.00 ms |
| **Q** | `15` | **Q**uarterly | Extended 1/4 (45°) | 6.25% | 1.25 ms |
| **H** | `33` | **H**alf | Extended 1/2 (90°) | 7.50% | 1.50 ms |
| **X** | `22` | e**X**tended | Fully Extended (180°) | 10.00% | 2.00 ms |

---

## 💻 Hardware Modules

* `vga_test.v`: Top-level wrapper integrating VGA controller, PS/2 reader, character ROM decoder, and PWM generator.
* `div_clk_100_2_25.v`: Clock divider generating the 25 MHz Pixel Clock from the 100 MHz FPGA oscillator.
* `div_clk_25Mhz_4Khz.v`: Frequency divider using cascading modulo-25 counters for precise PWM timing.
* `pwm.v`: Pulse-Width Modulation generator driving the servo pulse duration based on state inputs.
* `kbd_protocol.v`: Serial PS/2 decoder extended with key-release (`enable`) synchronization logic.
* `scan2Address.v`: Maps PS/2 scan codes to PWM comparator thresholds and character ROM address offsets.
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
