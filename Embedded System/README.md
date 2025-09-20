# Embedded Systems Overview

## 1. System Architecture

### UI Mini Jackal
- **Arduino UNO R3**: Microcontroller running low-level firmware.
- **Adafruit 16-channel PWM Servo Controller (PCA9685)**: Controls 12 MG-996R servos.
- **Servos**: Actuate each leg’s joints (3 DOF per leg).
- **Sensors**:
  - IMU: Orientation & motion feedback
  - Rotary encoders: Joint position feedback
- **Power Distribution**: Separate rails for logic (Arduino) and servos.
- **Firmware**: Handles low-level servo control and basic sensor reading.

### UI Jackal II
- **Raspberry Pi 5**: Central processor for high-level control, navigation, and autonomy.
- **Adafruit 16-channel PWM Servo Controller (PCA9685)**: Generates PWM for twelve F37490 servos.
- **Servos, IMU, Encoders, Touch Sensors**: Managed by Pi software for actuation and sensing.
- **Power Distribution**: Logic (Pi + sensors) separated from servo power.
- **Software**: Handles servo control commands, gait sequences, sensor fusion, and autonomous navigation.

---

## 2. Hardware Interfaces

| Component          | Mini Jackal                | Jackal II                  |
|-------------------|---------------------------|---------------------------|
| PWM Control        | Arduino → PCA9685         | Pi → PCA9685 (I²C)       |
| Servos             | 12 × MG-996R/F37490       | 12 × F37490              |
| IMU                |                           | I²C to Pi                |
| Rotary Encoders    |                           | GPIO to Pi               |
| Touch Sensors      |                           | Digital pins to Pi       |
| Power Rails        | Logic & servo separate    | Logic & servo separate   |


### Description of Connections

- **Raspberry Pi → PCA9685**: Connected via I²C (SDA/SCL pins) to send PWM commands.  
- **PCA9685 → Servos**: Each servo connected to one of the 12 PWM channels.  
- **Raspberry Pi → Sensors**:  
  - **IMU**: I²C connection for orientation and motion feedback.  
  - **Rotary Encoders**: GPIO pins for joint position sensing.  
  - **Touch Sensors**: Digital GPIO pins.  
- **Power Rails**:  
  - Servos powered by separate high-current rail.  
  - Raspberry Pi and sensors powered by separate logic rail.  

> **Tip:** For clarity, the wiring schematic for the UI Mini Jackal is found here: https://www.instructables.com/3D-Printed-Robot-Dog/, and it is shown for the UI Jackal II in the Hardware folder of this repository. 


---

## 3. Firmware / Software

### Mini Jackal
- **Language**: Arduino C++ (sketches)
- **Responsibilities**:
  - Generate PWM signals for servos
  - Read encoder and IMU data
  - Basic gait sequences
- **Libraries**: Arduino Servo library, Wire.h (I²C)

### Jackal II
- **Language**: Python (optionally ROS 2 nodes)
- **Responsibilities**:
  - Send servo commands to PCA9685 via I²C
  - Integrate sensor readings for feedback
  - Run gait generation, navigation, and autonomous behaviors
- **Libraries**: `adafruit-pca9685`, `smbus`, `RPi.GPIO`, `numpy`, `scipy`, ROS 2

---

## 4. Control Strategy

| Feature              | Mini Jackal                    | Jackal II                        |
|---------------------|--------------------------------|---------------------------------|
| Low-level servo control | Handled by Arduino firmware  | PCA9685 controlled by Pi software |
| Feedback integration | Arduino reads encoders & IMU  | Pi reads encoders, IMU, and touch sensors |
| High-level behavior  | Limited gait sequences         | Gait generation, navigation, autonomy |
| Expansion            | Hard to scale beyond Arduino   | Easily integrated with ROS, advanced algorithms |

*Mini Jackal demonstrates proof-of-concept low-level control; Jackal II consolidates everything on the Pi, enabling sophisticated autonomous behaviors.*

---

## 5. Testing & Debugging

### Mini Jackal
- Test each servo individually via Arduino IDE
- Calibrate neutral positions for joints
- Verify sensor readings (IMU, encoders)
- Log data on serial monitor for gait adjustments

### Jackal II
- Test PWM output to servos through Python scripts
- Verify sensor readings via I²C and GPIO
- Record joint angles, IMU data, touch sensors for gait tuning
- Monitor power draw to ensure stable operation
- Use ROS tools (if installed) for debugging autonomy
