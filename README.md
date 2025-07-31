# 💧 Automatic Water Level Controller Using 8051 Microcontroller

This project implements an **automatic water level controller** using the **8051 microcontroller** and an **ultrasonic sensor**. The system monitors water levels in a tank and activates a motor pump automatically when levels fall below a certain threshold. It includes real-time LCD display feedback and self-calibrating adjustments for accurate distance measurements.

---

## 🔧 Hardware Components

- **8051 Microcontroller (e.g., AT89C51)**
- **Ultrasonic Sensor (HC-SR04 or equivalent)**
- **16x2 LCD Display**
- **Water Pump or Motor (relay-controlled)**
- **Power Supply**
- **Wires and Connectors**
- **Resistors/Transistors for driving pump relay**

---

## 🔌 Pin Configuration

| Component      | Pin Description | Connection |
| -------------- | --------------- | ---------- |
| **LCD**        | `RS`            | `P1^0`     |
|                | `EN`            | `P1^2`     |
|                | `Data Lines`    | `P0`       |
| **Ultrasonic** | `Trigger`       | `P3^5`     |
|                | `Echo`          | `P3^2`     |
| **Pump**       | `Pump Control`  | `P2^0`     |

---

## ⚙️ Working Principle

1. **Ultrasonic Measurement:**

   - The sensor sends out a pulse via the `trig_pin`.
   - The echo is received at the `echo_pin`, and the **time delay** is used to compute the distance to the water surface.

2. **Distance to Water Level Mapping:**

   - The time captured by **Timer0** is used to calculate distance using:
     ```
     Distance (in cm) = Time / 59
     ```
   - We have assumed the length of the tank as 10 cm (ie, 10 ppd)
   - A calibration offset of 35 is applied to refine the measurements.

3. **LCD Display:**

   - The LCD shows the **current level** (in pseudo-points per deciliter - `ppd`) based on distance.

4. **Automatic Pumping:**
   - If the level is below 2 `ppd`, the pump is turned ON.
   - Pump continues until the level exceeds 9 `ppd`.
   - Once full, pump is turned OFF.

---

## 🧠 Code Overview

### `lcd_setup()`

Initializes the LCD for command and data mode using `lcd_cmd` and `lcd_dat`.

### `ultra_trigger()`

Sends a 10 μs pulse to the trigger pin to initiate ultrasonic measurement.

### `get_range()`

Captures the time taken for the echo pulse and calculates the distance using Timer0.

### `distance()`

Applies correction factors to the raw distance to get the effective level in the tank, then displays it on the LCD.

### `main()`

- Continuously checks tank level.
- Triggers pump operation based on thresholds (below 2 or above 9 `ppd`).
- Displays the current status on the LCD.

---

## 📋 Notes

- `delay()` is a microsecond-level delay using `NOP` loops.
- `DPTR` is used to compute 16-bit values from Timer0 high and low bytes.
- The system uses polling (not interrupts) to detect echo timing.
