# Circuit Description — Home Automation System

## Overview

The circuit uses an **Arduino UNO** as the central controller, reading from three sensors and driving two relay modules that switch a light bulb and a DC motor (fan).

---

## Sensors

### 1. PIR Motion Sensor (555-28027)
- Connected to **Digital Pin 8**
- Outputs HIGH when motion is detected, LOW otherwise
- Acts as the master gate — if no motion, all outputs are disabled regardless of other sensor values

### 2. LDR (Light Dependent Resistor)
- Connected to **Analog Pin A5** via a voltage divider with a fixed resistor
- Threshold: **550** (out of 1023)
- Below 550 → ambient light is LOW (dark) → light bulb should turn ON
- Above 550 → ambient light is HIGH (bright) → light bulb stays OFF

### 3. TMP36 Temperature Sensor
- Connected to **Analog Pin A4**
- Outputs analog voltage proportional to temperature
- Conversion formula:
  ```
  Voltage = (ADC / 1024) * 5V
  Temp(°C) = (Voltage - 0.5) * 100
  ```
- Threshold: **30°C**
- Above 30°C → fan relay activates
- Below 30°C → fan stays OFF

---

## Output Stage

### Relay 1 — Light Control
- Control pin: **Digital Pin 5**
- Switches the light bulb
- Driven HIGH when: motion detected AND LDR < 550 (dark)

### Relay 2 — Fan Control
- Control pin: **Digital Pin 6**
- Switches the DC motor (fan)
- Driven HIGH when: motion detected AND temperature > 30°C

Both relays are **LU-5-R** type (5V coil, rated 3A/125V AC or 3A/24V DC).

---

## Power Supply

- Arduino powered via USB
- External supply: **7V / 146mA** for relay and load circuit

---

## Serial Output

At each loop iteration, the following are printed to Serial Monitor (9600 baud):
- `x` → PIR state (0 or 1)
- `y` → LDR raw ADC value (0–1023)
- `z` → TMP36 raw ADC value → converted to °C

---

## Decision Table

| Motion (x) | LDR (y) | Temp (°C) | D5 (Light) | D6 (Fan) |
|------------|---------|-----------|------------|----------|
| 0 | any | any | OFF | OFF |
| 1 | < 550 | > 30 | ON | ON |
| 1 | < 550 | < 30 | ON | OFF |
| 1 | > 550 | > 30 | OFF | ON |
| 1 | > 550 | < 30 | OFF | OFF |
