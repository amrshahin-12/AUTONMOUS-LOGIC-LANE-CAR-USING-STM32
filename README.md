# AUTONMOUS-LOGIC-LANE-CAR-USING-STM32
This repository contains the firmware for a robot that moves **between two lines** (a "logic lane**) using IR sensors and incorporates color detection using an RGB color sensor. The robot is programmed to stay within the boundaries of the two lines and perform specific actions (e.g., stopping or making a U-turn) when it detects certain colors (red or green). The project is built using STM32CubeMX and the STM32 HAL library.

---

## Table of Contents
1. [Overview](#overview)
2. [Hardware Requirements](#hardware-requirements)
3. [Software Requirements](#software-requirements)
4. [Project Structure](#project-structure)
5. [Configuration](#configuration)
   - [Clock Configuration](#clock-configuration)
   - [GPIO Configuration](#gpio-configuration)
   - [Timer Configuration](#timer-configuration)
6. [Usage](#usage)
7. [Code Explanation](#code-explanation)
   - [Logic Lane Behavior](#logic-lane-behavior)
   - [Color Detection](#color-detection)
8. [Contributing](#contributing)
9. [License](#license)

---

## Overview
The robot uses:
- **IR Sensors**: To detect two parallel lines and stay within the "logic lane."
- **RGB Color Sensor**: To detect specific colors (red and green) and trigger actions.
- **STM32 Microcontroller**: To control motors, read sensor data, and execute logic.

The firmware is written in C and is designed to run on STM32 microcontrollers. The project includes an `.ioc` file for STM32CubeMX, which configures the clock, GPIO, and peripherals.

---

## Hardware Requirements
- STM32 Microcontroller (e.g., STM32F103C8T6)
- IR Sensors (for detecting the two lines)
- RGB Color Sensor (e.g., TCS3200)
- Motor Driver (e.g., L298N)
- DC Motors with Wheels
- Power Supply 
- Chassis for the Robot

---

## Software Requirements
- STM32CubeMX (for generating initialization code)
- STM32 HAL Library
- IDE (e.g., STM32CubeIDE, Keil, or any compatible IDE)

---

## Project Structure
The project includes the following files:
- `main.c`: Contains the main logic for staying within the logic lane and color detection.
- `main.h`: Header file for `main.c`.
- `stm32f1xx_hal_msp.c`: HAL MSP initialization and de-initialization file.
- `stm32f1xx_it.c`: Interrupt service routines.
- `system_stm32f1xx.c`: System initialization and clock configuration.
- `*.ioc`: STM32CubeMX configuration file.

---

## Configuration

### Clock Configuration
The clock configuration is set up using STM32CubeMX. Below is a screenshot of the clock configuration:

![WhatsApp Image 2025-03-05 at 15 20 14_a0a70543](https://github.com/user-attachments/assets/3768d9cc-5350-47b4-a856-c5522e04e414)


### GPIO Configuration
The GPIO pins are configured for:
- IR Sensors (Input)
- Motor Driver Control (Output)
- RGB Color Sensor Control (Output)
- RGB Color Sensor Outpin (Input)

Below is a screenshot of the GPIO configuration:

![WhatsApp Image 2025-03-05 at 15 05 09_b2be16a5](https://github.com/user-attachments/assets/9489f96f-983f-4f96-80c9-f42ee5da6025)

Note: The GPIO configuration file(.ioc) is already included in the repository!
### Timer Configuration
The timers are configured for:
- PWM generation (for motor control)
- Input capture (for color sensor pulse measurement)

Below is a screenshot of the timer configuration:

![WhatsApp Image 2025-02-23 at 14 15 57_fc4a85a8](https://github.com/user-attachments/assets/42bc7741-77de-4d23-8e4f-8792a8839c99)
![WhatsApp Image 2025-02-23 at 14 15 57_9faaf1f5](https://github.com/user-attachments/assets/3130a2d1-05e6-4b2c-b79c-af4e5b88d63f)

---

## Usage
1. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/logic-lane-robot.git
   ```
2. Open the project in STM32cube IDE.
3. Build the project.
4. Flash the firmware to your STM32 microcontroller.
5. Power on the robot and place it between the two lines to start moving.

---

## Code Explanation

### Logic Lane Behavior
The robot uses **four IR sensors** to detect the boundaries of the logic lane. It adjusts its movement to stay centered between the two lines:
- If the **left sensors** detects a line, the robot moves **right** to center itself.
- If the **right sensors** detects a line, the robot moves **left** to center itself.
- If **both sensors** detect the lines, the robot moves **forward**.
- If **neither sensor** detects a line, the robot stops or searches for the lane.

```c
void move() {
    int s = 170; // Base speed
    if ((IR_LEFT == 0) && (IR_RIGHT == 0)) {
        // Both sensors detect the lines - move forward
        moveForward(s, s);
    } else if (IR_LEFT == 0) {
        // Left sensor detects the line - move right
        moveright(s, s);
    } else if (IR_RIGHT == 0) {
        // Right sensor detects the line - move left
        moveleft(s, s);
    } else {
        // Neither sensor detects the line - stop
        stopmove();
    }
}
```

### Color Detection
The robot uses an RGB color sensor to detect specific colors (red and green). When a color is detected, the robot performs predefined actions:
- **Green**: Triggers a U-turn after completing 2 full laps.
- **Red**: Stops for a set duration before continuing.

```c
while (1) {
    readColorSensor(&red, &green, &blue, &clear);

    if (red > 1.15 * green && blue > 0.85 * green && green > clear) {
        // GREEN detected
        if (uturn == 1) {
            uturnfunc(200, 200); // Perform U-turn
            HAL_Delay(1400);
            stopmove();
        }
    } else if (red * 1.6 < green && blue > red * 1.2 && red > clear) {
        // RED detected
        stopmove();
        HAL_Delay(5000); // Stop for 5 seconds
    } else {
        // No specific color detected - continue logic lane behavior
        move();
    }
}
```

---
## 🏆 Team & Awards

This project was developed as a team submission for [Cairo University Eco Racing Team], where it placed **4th place**.

📄 [View Team Certificate](./certificates/4th_place_team_certificate_amr.pdf)

### 👥 Team Members
- [@amrshahin-12](https://github.com/amrshahin-12) 
- [@FarahhYehya](https://github.com/FarahhYehya)
- [@omar55221](https://github.com/omar55221).
- Omar Nagy
- Ward Salkini
- Fatma Salim
---

## Contributing
Contributions are welcome! Please feel free to submit a pull request or open an issue if you find any bugs or have suggestions for improvements.

---

## License
This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---
