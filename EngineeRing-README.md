# EngineeRing — Real-Time BLE Gesture-Control Ring

> A wearable smart ring that translates mid-air hand gestures into real-time OS-level cursor control. Built as a Senior Design project at San Diego State University.

📄 [User Manual](./EngineeRing-User-Manual.pdf) · 🖼️ [Senior Design Poster](./Senior-Design-Poster.pdf) · 🌐 [Portfolio Writeup](https://reemoawad.github.io/My-Portfolio/#projects)

---

## The Problem

Standard HCI input devices (mouse, touchpad) fail users with motor impairments or in hands-free contexts. Existing gesture devices are either expensive, tethered, or too coarse for precision tasks like cursor navigation and typing.

## The Solution

EngineeRing is a ring form-factor wearable that captures IMU motion data, transmits it over BLE, and drives OS-level input in real time — enabling cursor navigation, gesture typing, presentation control, and rehabilitation-focused motion tracking from a single device worn on one finger.

## My Role

**Sole software engineer.** I owned the entire software stack — from firmware communication and BLE packet handling to sensor fusion, gesture classification, input dispatch, and the application-level UI. The hardware team handled PCB design and sensor selection; everything running in software was designed and built by me.

---

## System Architecture

```
IMU (Accelerometer + Gyroscope)
        │
        ▼
  BLE Peripheral (Ring)
        │  BLE GATT notifications
        ▼
  Python BLE Host (PC)
        │
        ├─ Sensor Fusion (complementary filter / Madgwick)
        ├─ Motion Smoothing + Jitter Reduction
        ├─ Gesture Classification (threshold + state machine)
        │
        ▼
  OS Input Dispatch (PyAutoGUI / OS-level APIs)
        │
        ├─ Cursor Navigation
        ├─ Gesture Typing
        ├─ Presentation Control
        └─ Rehab Motion Tracking
```

---

## Technical Depth

### Sensor Fusion
Raw IMU data from the accelerometer and gyroscope is fused using a complementary filter to produce stable orientation estimates. This eliminates the drift inherent in integrating gyroscope data alone while preserving responsiveness to fast motion.

### Jitter Reduction & Smoothing
Cursor movement applies exponential moving average smoothing with a tunable gain parameter. The smoothing coefficient was calibrated empirically to balance precision (low jitter at rest) against responsiveness (fast tracking during intentional motion) — a tradeoff that directly affects usability for fine tasks like clicking targets.

### BLE Packet Handling
IMU samples are streamed as BLE GATT notifications. The host handles packet loss, reordering, and rate variation — maintaining stable input even under real-world BLE interference conditions.

### Gesture Classification
Gestures are classified via a threshold-and-state-machine approach trained on the specific motion signatures of ring-worn IMU data. Recognizable gestures include discrete clicks, swipes, and orientation holds, each mapped to configurable OS actions.

### Interaction Modes
The system supports runtime mode switching between:
- **Pointer mode** — continuous cursor control
- **Gesture typing** — discrete character input via finger motions
- **Presentation mode** — slide advance/rewind gestures
- **Rehab mode** — motion capture for range-of-motion tracking and repetition counting

---

## Technologies

| Layer | Stack |
|---|---|
| Language | Python 3 |
| BLE Communication | `bleak` (async BLE GATT client) |
| Sensor Fusion | Custom complementary filter |
| OS Input | `pyautogui`, `pynput` |
| Data Processing | `numpy` |
| Hardware | Custom PCBA with IMU + BLE SoC |

---

## Key Results

- Sub-20ms end-to-end latency from gesture to OS input under normal BLE conditions
- Stable cursor tracking with jitter reduced to imperceptible levels at rest
- Successfully demonstrated across all four interaction modes in live Senior Design presentation
- Designed to support rehabilitation use cases — validated with structured motion tracking exercises

---

## Status

Completed as a Senior Design capstone project. The software architecture is designed to be portable to any BLE IMU device with minor configuration changes.
