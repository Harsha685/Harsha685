<div align="center">

# Chelluri Harsha Vardhan

### Robotics & Embedded Systems Engineer

Bare-metal firmware · Sensor fusion · ROS 2 · Systems programming

[![Email](https://img.shields.io/badge/Email-harshavardhanchelluri%40gmail.com-D14836?style=flat-square&logo=gmail&logoColor=white)](mailto:harshavardhanchelluri@gmail.com)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Harshavardhan%20Chelluri-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/harshavardhan-chelluri-77194b382)
[![Portfolio](https://img.shields.io/badge/Portfolio-Visit-111111?style=flat-square&logo=vercel&logoColor=white)](https://harsha-github-io-iota.vercel.app/)

**Seeking robotics / embedded internships, especially with startups in Japan and Germany.**

</div>

---

## Hey, I'm Harsha

I'm a Computer Science and Systems Engineering student at Andhra University College of Engineering (AUCE), Visakhapatnam, and a Robotics Intern at Aegion Dynamic Solutions.

I like working close to the hardware: register-level C on STM32 and RP2040, FreeRTOS on dual-core MCUs, Kalman-filter sensor fusion, and ROS 2 navigation stacks. The problems I enjoy most are the ones where timing, noise, and physical constraints decide whether a system actually works, and I try to measure before I claim it does.

Right now I'm building a flight controller from scratch. Long term, I'm learning Japanese and aiming for graduate research in robotics in Japan, reading into SLAM and embedded ML along the way.

---

## Experience

### Robotics Intern · Aegion Dynamic Solutions LLP
*Visakhapatnam, India · April 2026 – Present*

**Railway relay test rig (firmware lead)**
- Leading firmware for an **ESP32**-based device that validates **QN1-series railway relays**
- Implemented an **18-point electrical and timing test suite**: coil resistance, pickup/drop voltage, contact gap and resistance, bounce and transfer time, insulation resistance, and reverse polarity
- Live test results rendered on an **HMI touchscreen** display

**Autonomous floor-coverage robot (simulation)**
- Built an end-to-end explore → map → plan → sweep pipeline for a cleanroom-style cleaning robot in **ROS 2 Jazzy**, **Gazebo**, and **Nav2**, reaching **94–97% ground-truth floor coverage** ([details below](#2-autonomous-floor-coverage-robot))

---

## Featured Projects

### 1. Flight Controller &nbsp;`in progress`

A custom flight controller built from the ground up: drivers, RTOS scheduling, state estimation, and hardware.

- **Bare-metal C drivers** for GPIO, UART, SPI, I²C, timers, and RTC, with custom startup code and linker scripts
- **FreeRTOS SMP** prototype on RP2040 (Raspberry Pi Pico W), with the sensor task pinned to Core 1 and reporting isolated on Core 0
- **Sensor fusion** of IMU, barometer, and magnetometer through a hand-tuned **Kalman filter**
- Hand-soldered and prototyped hardware; debugged with ST-Link, multimeter, and oscilloscope
- **Next:** DMA-driven sensor reads, PID attitude control, and porting to STM32F411

<details open>
<summary><b>Case study: 100 Hz control-loop jitter profiling (RP2040 + FreeRTOS)</b></summary>

<br>

Built a profiling harness to verify that the sensor loop actually held its 100 Hz (10,000 µs) period under `vTaskDelayUntil`, measuring 1,000 consecutive intervals with `time_us_64()`.

| Metric   | I²C @ 100 kHz | I²C @ 400 kHz |
| -------- | ------------: | ------------: |
| Min      | 3,390 µs      | 9,095 µs      |
| Max      | 10,098 µs     | 10,201 µs     |
| Average  | 9,992 µs      | 9,989 µs      |
| Outliers | 1 / 1,000     | 0 / 1,000     |

**Finding:** blocking I²C sensor reads at 100 kHz occasionally overran the period, causing a catch-up interval of 3,390 µs.
**Fix:** raising the bus to 400 kHz cut the read overhead and eliminated the observed outliers.

</details>

---

### 2. Autonomous Floor-Coverage Robot

A simulated cleaning robot of the kind used in pharma cleanrooms. Placed on its dock in a room it has never seen, it explores, maps, plans full coverage, sweeps the floor, and grades its own work, all from one command.

**Stack:** ROS 2 Jazzy · Gazebo · TurtleBot4 · Nav2 · slam_toolbox · Fields2Cover · Python · Docker

| World | Floor swept (ground truth, reachable floor) |
|---|---:|
| Two rooms joined by a doorway | **95.0%** |
| Warehouse room, dock in the middle | **96.9%** |
| Warehouse room, dock at one end, priority-ordered | **94.0%** (60/61 paths, 32 min) |

Localization error against ground truth: **1.4 cm median**, 3.3 cm 95th percentile.

- **Frontier exploration** driven through Nav2's `NavigateToPose`, with reachability checks that reject fake frontiers created by LiDAR rays leaking through walls
- **Custom occupancy map** built from hit/pass voting on raw scans at the SLAM pose, which keeps obstacle faces that slam_toolbox's grid erased; mapped 99% of the real floor
- **8-stage map filter** (leak-zone removal, despeckle, gap closing, reachability, dock burn-in, wall shell) so AMCL localizes reliably on the saved map
- **Coverage planning with region priority:** Fields2Cover boustrophedon swaths, split into bands by Dijkstra walking distance from the entrance so the dirtiest floor is cleaned first (or last, per GMP cleanroom practice)
- **Fault-tolerant executor** using `FollowPath` with a Rotation Shim + Regulated Pure Pursuit controller, plus guards for wall escape, stall watchdog, skip-ahead, Nav2 lifecycle restart, and lost localization
- Diagnosed and fixed simulator issues including a faulty LiDAR sector, Twist/TwistStamped mismatch, and Docker's 64 MB `/dev/shm` silently dropping DDS messages mid-run

---

### 3. FlashBot &nbsp;·&nbsp; [Repo](https://github.com/Harsha685/Flash_Bot) · [Docs](https://flash-bot-docs.vercel.app/)

An open-source CLI that detects a connected board, compiles the right firmware, and flashes it with no IDE in the loop.

- Supports **AVR, ESP32, and RP2040**, with Dockerized cross-compilation toolchains (`arduino-cli`, `esptool`)
- Abstracts USB/UART device handling with `pyudev` for consistent behavior across boards
- Event-driven state machine with SQLite logging for a traceable record of every flash
- About **25% faster** end-to-end compile-and-flash cycles than Arduino IDE and PlatformIO in my benchmarks
- Picked up by users after I shared it on Reddit, with positive feedback
- **Roadmap:** PlatformIO integration and STM32 support

<div align="center">
<img src="https://raw.githubusercontent.com/Harsha685/Harsha685/main/Flashbot-ezgif.com-video-to-gif-converter.gif" width="420"/>
</div>

---

### 4. CHIP-8 Emulator &nbsp;·&nbsp; [Repo](https://github.com/Harsha685/chip-8-emulator)

A CHIP-8 interpreter written from scratch in C with SDL.

- Full fetch-decode-execute cycle with bitwise opcode decoding
- Emulated memory map, registers, stack, and timers
- Display rendering and real-time keypad input
- **Next:** SUPER-CHIP extension support

<div align="center">
<img src="https://raw.githubusercontent.com/Harsha685/Harsha685/main/Chip8%20(1).gif" width="420"/>
</div>

---

## More Work

| Project | What it is | Stack |
|---|---|---|
| **Offline AI Desktop Companion** · *1st Place, Srujana 2026* | A robotic desktop buddy running a fully offline voice pipeline (Whisper → local LLM via Ollama → TTS) with English and Telugu support. It greets people it sees through a camera and animates a "speaking" face on a TFT display. I built the embedded hardware (ESP32 control, servo actuation, TFT) and the integration layer connecting the pipeline to the robot. | ESP32, servos, TFT, Python, Whisper, Ollama |
| **Drone Survey Simulation** · *3rd Place, SIH Internal Round* | Autonomous drone survey missions over Gazebo models of Visakhapatnam buildings: mission planning, point-cloud accumulation, and digital surface model (DSM) generation. Fully containerized with a patched PX4 drone model. | ROS 2 Jazzy, Gazebo Harmonic, PX4 SITL 1.15, MAVROS, Docker |
| **Signal vs. Event-Based Job Control in POSIX Shells** · *research, design stage* | Independent study building two POSIX-compliant shells to compare job-control models under high-concurrency workloads. | C, POSIX |

---

## Technical Skills

| Area | Tools & Technologies |
|---|---|
| **Languages** | C (bare-metal), C++, Python, Rust, MATLAB/Simulink |
| **Microcontrollers** | STM32F411 (Black Pill), RP2040 (Pico / Pico W), ESP32 (incl. S3, C3), AVR |
| **Firmware** | Register-level drivers (GPIO, UART, SPI, I²C, timers, RTC), FreeRTOS (SMP), startup code & linker scripts |
| **Robotics** | ROS 2 Jazzy, Nav2, slam_toolbox, AMCL, Fields2Cover, PX4 SITL, MAVROS, Gazebo (Harmonic), Isaac Sim, Kalman filtering, LiDAR & camera integration |
| **Hardware** | Soldering & prototyping, HMI touchscreens, ST-Link, oscilloscope, multimeter, KiCad, Fusion 360 |
| **Tooling** | Linux, Git, Docker, STM32CubeIDE, OpenCV, NumPy |

---

## Currently Building & Learning

- **Flight controller:** DMA-based sensor pipelines and PID attitude control
- **Custom particle filter** for localization in my robotics simulation project
- **Embedded Rust** on microcontrollers
- **Japanese:** around JLPT N4 level

---

## Education

**Andhra University College of Engineering (AUCE)**, Visakhapatnam
B.Tech, Computer Science and Systems Engineering · 2025 – 2029
CGPA **8.15 / 10** (Sem 1: 8.00 · Sem 2: 8.45)

Relevant coursework: Data Structures in C, Digital Logic Design, Electronics, Operating Systems, Linux

---

## Achievements

- 🥇 **1st Place**, Srujana 2026 State-Level Hackathon, SVPEC Visakhapatnam
- 🥉 **3rd Place**, Smart India Hackathon (Internal Round)
- 🎓 **PMSS Scholarship** recipient

---

<div align="center">

Always happy to talk about robotics, embedded systems, or edge AI.
**[Email](mailto:harshavardhanchelluri@gmail.com)** · **[LinkedIn](https://www.linkedin.com/in/harshavardhan-chelluri-77194b382)**

</div>
