# ESP32 Microprocessor-Based Automatic Door Opener

A smart, automated door opener system using an ESP32 microprocessor that detects when a person is near and automatically opens the door for them.

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Hardware Requirements](#hardware-requirements)
- [Software Requirements](#software-requirements)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Architecture](#architecture)
- [Safety Features](#safety-features)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

## Overview

This project implements an intelligent door opening system powered by the ESP32 microcontroller. The system continuously monitors for nearby persons and automatically triggers the door opening mechanism when motion or proximity is detected, making it ideal for accessibility, convenience, and contactless entry solutions.

## Features

- ✅ Automatic door detection and opening
- ✅ Motion/proximity sensing
- ✅ ESP32-based control system
- ✅ Low power consumption
- ✅ Wireless connectivity ready
- ✅ Configurable sensitivity settings
- ✅ Real-time status monitoring
- ✅ Safety protocols and timeout mechanisms
- ✅ IoT-enabled with Firebase cloud integration
- ✅ Remote trigger capability via mobile app

## Hardware Requirements

### Core Components

- **ESP32 Microcontroller** - Main processing unit
- **Proximity/Motion Sensor** - Detects nearby persons
  - Options: PIR sensor, ultrasonic sensor, or IR sensor
- **Door Actuator** - Electric door lock or motor
  - Solenoid lock, servo motor, or stepper motor
- **Power Supply** - Suitable for ESP32 and door actuator
  - Recommended: 12V or 5V regulated power supply
- **Relay Module** - Controls high-power door actuator
- **Jumper Wires** and breadboard for prototyping

### Optional Components

- OLED display for status indication
- Wi-Fi/Bluetooth modules (ESP32 has built-in)
- Additional sensors (temperature, humidity)
- LED indicators for system status
- Firebase Real-time Database for cloud connectivity

## Software Requirements

- Arduino IDE or PlatformIO
- ESP32 Board Support Package
- Required Libraries:
  - `ESP32` core libraries
  - Sensor-specific libraries (e.g., `DHT.h`, `Ultrasonic.h`)
  - `WiFi.h` for connectivity
  - `Firebase.h` for cloud integration (optional)
  - Other project-specific dependencies

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/albrud199/ESP32---Microprocessor-based-automatic-door-open.git
cd ESP32---Microprocessor-based-automatic-door-open
```

### 2. Set Up Arduino IDE

- Download and install [Arduino IDE](https://www.arduino.cc/en/software)
- Go to **File** → **Preferences**
- Add the ESP32 board manager URL:
  ```
  https://dl.espressif.com/dl/package_esp32_index.json
  ```
- Go to **Tools** → **Board Manager**, search for "ESP32", and install the latest version

### 3. Install Dependencies

- Open Arduino IDE Library Manager (**Sketch** → **Include Library** → **Manage Libraries**)
- Search for and install required libraries for your sensors and components

### 4. Upload Code to ESP32

- Connect ESP32 to your computer via USB
- Select the correct board and port in Arduino IDE
- Open the main sketch file
- Click **Upload**

## Configuration

Edit the configuration section in the sketch to customize:

```cpp
// Sensor Settings
#define SENSOR_PIN 32              // GPIO pin for motion/proximity sensor
#define SENSOR_TYPE ULTRASONIC     // PIR, ULTRASONIC, or IR

// Door Actuator Settings
#define DOOR_PIN 27                // GPIO pin for servo/motor control
#define DOOR_OPEN_DURATION 3000    // Duration to keep door open (ms)

// Servo Motor Configuration
#define SERVO_MIN_ANGLE 0          // Fully closed position
#define SERVO_MAX_ANGLE 90         // Fully open position

// Sensitivity Settings
#define DETECTION_THRESHOLD 50     // Distance threshold in cm (for ultrasonic)
#define DETECTION_TIMEOUT 5000     // Time before door auto-closes (ms)

// Wi-Fi Settings
#define WIFI_SSID "YOUR_SSID"
#define WIFI_PASSWORD "YOUR_PASSWORD"

// Firebase Settings (Optional)
#define FIREBASE_HOST "your-firebase.firebaseio.com"
#define FIREBASE_AUTH "your-firebase-auth-token"
```

## Usage

### Basic Operation

1. **Power On**: Connect the ESP32 to power supply
2. **Initialization**: System performs self-check (check serial monitor)
3. **Monitoring**: System continuously monitors for motion/proximity
4. **Detection**: When motion is detected, door opens automatically
5. **Auto-Close**: Door closes after configured timeout

### Serial Monitor Output

Connect to the ESP32 via serial (115200 baud) to see real-time status:

```
[INFO] System initialized
[INFO] Sensors ready
[INFO] Connected to WiFi
[DEBUG] Motion detected!
[ACTION] Opening door...
[INFO] Door open (90°)
[DEBUG] Timeout reached...
[ACTION] Closing door...
[INFO] Door closed (0°)
```

### Remote Control via Firebase

- Update the `/door/command` value in Firebase Realtime Database to trigger door remotely
- Check `/door/status` for current door state
- Subscribe to `/sensor/distance` for real-time sensor readings

### Manual Override

- Press the reset button on ESP32 to restart the system
- Use the physical button (if implemented) to manually control the door

## Architecture

### System Flow

```
Power On
  ↓
Initialize WiFi & Firebase Connection
  ↓
Initialize Sensors & Servo Motor
  ↓
Main Loop: Continuous Monitoring
  ├─ Read Ultrasonic Sensor
  ├─ Check Firebase for Remote Commands
  ├─ Compare Distance with Threshold
  └─ If Motion Detected OR Remote Command:
      ├─ Activate Servo Motor (Open)
      ├─ Wait (DOOR_OPEN_DURATION)
      ├─ Move Servo Motor (Close)
      └─ Log Action to Firebase
  ↓
Repeat Loop
```

### Pin Configuration

| Component | GPIO Pin | Function |
|-----------|----------|----------|
| Ultrasonic Sensor (Trig) | 5 | Output (Pulse) |
| Ultrasonic Sensor (Echo) | 18 | Input (Pulse) |
| Servo Motor Control | 27 | Output (PWM) |
| Serial RX | 3 | UART Receive |
| Serial TX | 1 | UART Transmit |

## Safety Features

- **Timeout Mechanism**: Door automatically closes after set duration
- **Sensor Validation**: Multiple readings to prevent false triggers
- **Power Protection**: Relay/transistor prevents direct power drain on GPIO
- **Manual Override**: Always possible to manually control the door
- **Status Monitoring**: Serial feedback and Firebase logging for debugging
- **Fail-Safe Design**: Door defaults to closed state on power loss
- **Distance Hysteresis**: Prevents jittering at threshold boundaries

## Troubleshooting

### Door Not Opening

- **Check Power Supply**: Ensure ESP32 and servo have adequate power (5-12V)
- **Verify Pin Configuration**: Confirm GPIO pins match your wiring
- **Test Sensor**: Use serial monitor to see if motion is detected
- **Check Servo**: Test servo manually with a simple test sketch
- **Verify Firebase Connection**: Check if remote commands are being received

### Sensor Not Detecting

- **Adjust Sensitivity**: Lower the `DETECTION_THRESHOLD` value
- **Clean Sensor**: Remove dust/obstructions from ultrasonic sensor
- **Check Wiring**: Verify sensor connections are secure
- **Test Sensor Directly**: Use a simple test sketch to verify sensor output
- **Check Distance**: Ensure object is within detection range

### WiFi Connection Issues

- **Verify Credentials**: Double-check SSID and password
- **Check Signal Strength**: Move ESP32 closer to router
- **Update Firmware**: Flash latest ESP32 firmware
- **Reset WiFi**: Restart your router and ESP32

### Serial Monitor Not Showing Output

- **Check Baud Rate**: Must be 115200 in serial monitor
- **Verify USB Connection**: Try different USB port
- **Reset ESP32**: Press the RESET button
- **Check Code**: Ensure `Serial.begin(115200)` is in setup

## Project Insights

This project demonstrates:
- ESP32 microcontroller programming with Arduino
- Ultrasonic sensor interfacing and distance measurement
- Servo motor control and PWM signal generation
- IoT integration with Firebase Realtime Database
- Wireless communication and cloud-based automation
- Real-time monitoring and remote triggering

## Contributing

Contributions are welcome! To contribute:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/YourFeature`)
3. Commit your changes (`git commit -m 'Add YourFeature'`)
4. Push to the branch (`git push origin feature/YourFeature`)
5. Open a Pull Request

Please ensure your code follows best practices and includes comments.

## License

This project is open source and available under the [MIT License](LICENSE).

## Support & Contact

For questions, issues, or suggestions:
- Open an issue on GitHub
- Contact: albrud199

---

**Last Updated**: June 2026

**Status**: Active Development ✅

**IoT-Enabled** with Firebase Cloud Integration 🌐

Happy automating! 🚪✨
