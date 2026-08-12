# 💧 Automated Water Tank — IoT-Based Ultrasonic Water Level Monitoring & Auto-Fill System

An IoT-enabled water level monitoring system built around the ESP32 microcontroller, an HC-SR04 ultrasonic sensor, an RGB LED, a buzzer, and the Blynk IoT platform. The system continuously measures tank water level using non-contact ultrasonic sensing, classifies it into LOW / MEDIUM / FULL states, reflects that status locally through an RGB LED and buzzer, and pushes live readings to a Blynk dashboard for remote monitoring.

## ✨ Features

- Non-contact water level sensing using an HC-SR04 ultrasonic sensor
- Real-time classification into LOW, MEDIUM, and FULL tank states
- Local status indication via a tri-color RGB LED
- Audible double-beep buzzer alert on FULL and on critical-low conditions
- Live remote dashboard over Wi-Fi via Blynk (Gauge, Label, and LED widgets)
- Derived motor-status flag showing when refilling is required
- Compact, low-cost circuit with no display hardware required

## 🧰 Components Required

| Component | Quantity / Specification |
|---|---|
| ESP32 Dev Board (WROOM-32) | 1 |
| HC-SR04 Ultrasonic Sensor | 1 |
| RGB LED (Common Cathode) | 1 |
| Buzzer (Active, 3.3V/5V) | 1 |
| Resistors (220Ω) | 3 (RGB LED channels) |
| Breadboard | 1 |
| Jumper Wires (M-M / M-F) | As required |
| USB-C Cable | 1 |
| Wi-Fi Network (2.4 GHz) | For ESP32 connectivity |
| Blynk Account + Blynk IoT Console | Free tier is sufficient |
| Water Container / Tank (prototype) | 1 |

## 🔌 Circuit Connections

| Component Pin | ESP32 Pin |
|---|---|
| HC-SR04 VCC | 5V (VIN) |
| HC-SR04 GND | GND |
| HC-SR04 TRIG | GPIO 5 |
| HC-SR04 ECHO | GPIO 18 (via voltage divider — ECHO is 5V, ESP32 GPIO is 3.3V tolerant) |
| RGB LED — Red | GPIO 25 (via 220Ω resistor) |
| RGB LED — Green | GPIO 26 (via 220Ω resistor) |
| RGB LED — Blue | GPIO 27 (via 220Ω resistor) |
| RGB LED — Common Cathode | GND |
| Buzzer (+) | GPIO 14 |
| Buzzer (−) | GND |

> ⚠️ Note: The HC-SR04 ECHO pin outputs 5V, while ESP32 GPIOs are 3.3V tolerant only. A resistor voltage divider (e.g., 1kΩ and 2kΩ) on the ECHO line is recommended to protect the GPIO.

## ⚙️ Working Principle

The HC-SR04 emits a 40 kHz ultrasonic pulse from the TRIG pin and measures the echo return time on the ECHO pin. This is converted to a distance:

```
Distance (cm) = (duration × 0.0343) / 2
```

Using the known tank height, the ESP32 converts this distance into a water level percentage:

```
Water Level (%) = ((Tank Height − Measured Distance) / Tank Height) × 100
```

The level, status label, and a derived motor-status flag are written every second to Blynk virtual pins V0, V1, and V2, updating the dashboard live.

## 🚦 Level Classification Logic

| Distance / Water Level | Tank Status | RGB LED | Buzzer | Motor Status (V2) |
|---|---|---|---|---|
| Distance > 90% of tank height (Level ≤ 30%) — Critical/Low | LOW | Red — solid ON | ON — 2 beeps | ON (1), steady |
| Level 31%–70% | MEDIUM | Blue — solid ON | OFF | ON (1), steady |
| Level 71%–94% | FULL | Green — solid ON | ON — 2 beeps | OFF (0) |

When the distance exceeds 90% of the tank height, the Motor Status widget is held steady ON rather than blinked, since `Blynk.virtualWrite()` sets a fixed value rather than a flashing state. The same double-beep alert is used for both the critical-low and FULL extremes.

## 📊 Blynk Dashboard Setup

1. Create a Template on the Blynk Console named "Automated Water Tank" for the ESP32 (Wi-Fi).
2. Datastream V0 — Integer (0–100), "Water Level" — bound to a Gauge widget.
3. Datastream V1 — String, "Tank Status" — bound to a Label widget (displays LOW / MEDIUM / FULL).
4. Datastream V2 — Integer (0/1), "Motor Status" — bound to an LED widget (lit while refilling is needed).
5. Copy the Template ID, Template Name, and Auth Token from the Device Info tab into the sketch.

## 📚 Required Libraries

- Blynk (by Volodymyr Shymanskyy, via Arduino Library Manager)
- ESP32 board package (by Espressif Systems, via Boards Manager)
- WiFi.h (bundled with the ESP32 board package)

## ✅ Advantages

- Non-contact sensing avoids corrosion and electrical risk from submerged probes
- Live remote monitoring viewable from anywhere with Wi-Fi/internet
- Simple three-colour RGB status indication, intuitive at a glance
- Audible confirmation when the tank reaches a sufficiently full state
- Compact circuit — no display hardware required, keeping cost and wiring minimal

## ⚠️ Limitations

- Motor status is derived from level thresholds only; no physical motor/pump driver is wired in this build
- Requires a stable 2.4 GHz Wi-Fi connection (ESP32 does not support 5 GHz networks)
- Ultrasonic accuracy can be affected by turbulence, foam, or vapour on the water surface
- Wi-Fi credentials and the Blynk auth token are hardcoded in the sketch and should be secured before deployment

## 🌍 Applications

- Domestic overhead/underground water tank monitoring
- Remote farm or agricultural reservoir monitoring
- Industrial storage tank status tracking with cloud visibility
- Educational demonstration of ESP32 + Blynk IoT dashboard integration

## 🚀 Future Scope

- Add a relay-driven motor/pump for a complete closed-loop auto-fill system
- Move Wi-Fi and Blynk credentials to a secure config rather than hardcoding
- Add historical data logging and threshold alerts within the Blynk app

## 👤 Author

**M. Jayantha Siva Srinivas**
B.Tech, Electronics and Communication Engineering
