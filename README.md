# Experiment: Design and Implementation of a Water Level Indicator Using STM32

## 1. Aim

To design and implement a water level indicator using the STM32 Nucleo-L031K6 microcontroller to measure the water level using an analog input, display the water level as LOW, MEDIUM, or HIGH on the Serial Monitor, and blink an LED when an overflow condition is detected.

## 2. Apparatus Required

1. STM32 Nucleo-L031K6 development board
2. Potentiometer (to simulate the water-level sensor)
3. Red LED
4. 220 Ω resistor
5. Connecting wires
6. Computer with internet access
7. Wokwi online simulator
8. STM32 HAL development environment
9. Virtual Serial Monitor

## 3. Theory

A water level indicator is an electronic system used to monitor the amount of water in a tank.

In this experiment, a potentiometer is used to simulate a water-level sensor. It generates a variable analog voltage between 0 V and 3.3 V. This voltage is applied to the PA0 analog input of the STM32 Nucleo-L031K6.

The STM32 microcontroller contains a 12-bit Analog-to-Digital Converter (ADC), which converts the analog voltage into a digital value ranging from 0 to 4095.

The water-level percentage is calculated using:

**Water Level (%) = (ADC Value × 100) / 4095**

The water level is classified into three categories:

- 0–30%: LOW
- Above 30% to 70%: MEDIUM
- Above 70%: HIGH

When the water level exceeds 70%, the red LED blinks to indicate a high-water-level or overflow warning. The measured value and status are displayed on the Serial Monitor using UART communication.

## 4. Pin Configuration

| Component | STM32 Connection | Function |
|---|---|---|
| Potentiometer VCC | 3V3 | Power supply |
| Potentiometer GND | GND | Ground |
| Potentiometer SIG | PA0 / ADC input | Analog input |
| Red LED anode | GPIO output through 220 Ω resistor | Overflow indication |
| Red LED cathode | GND | Ground |
| UART TX | Configured UART TX pin | Serial transmission |
| UART RX | Configured UART RX pin | Serial reception |

**Note:** Verify the actual GPIO and UART pin mapping for the selected Wokwi project before compiling the STM32 HAL program.

## 5. Block Diagram

```text
        Potentiometer
      (Water Level Sensor)
              |
              | Analog Voltage
              v
        PA0 / ADC Input
              |
              v
    +-----------------------+
    | STM32 Nucleo-L031K6   |
    |                       |
    | ADC Conversion        |
    | Water Level (%)       |
    | LOW / MEDIUM / HIGH   |
    +-----------+-----------+
                |
          +-----+-----+
          |           |
          v           v
     UART Serial    GPIO Output
        Monitor         |
                        v
                    Red LED
                 (Overflow Alert)
```

## 6. Algorithm

1. Start the program.
2. Initialize the STM32 HAL library.
3. Configure the system clock.
4. Configure PA0 as an analog input.
5. Initialize ADC1 and UART.
6. Configure the LED GPIO pin as an output.
7. Read the analog value from the potentiometer.
8. Convert the ADC reading into a percentage.
9. If the percentage is 30% or less, display LOW and turn off the LED.
10. If the percentage is above 30% and up to 70%, display MEDIUM and turn off the LED.
11. If the percentage is above 70%, display HIGH and blink the LED.
12. Display the ADC value, percentage, and status on the Serial Monitor.
13. Repeat the process continuously.

## 7. Program

The firmware is written for an STM32 HAL project. It requires the STM32 HAL drivers and the correct board-specific clock, ADC, GPIO, and UART configuration.

The core water-level classification and overflow LED logic is:

```c
percentage = (waterLevel * 100U) / 4095U;

if (percentage <= 30U)
{
    sendMessage("Status: LOW\r\n");
    HAL_GPIO_WritePin(LED_GPIO_Port, LED_Pin, GPIO_PIN_RESET);
}
else if (percentage <= 70U)
{
    sendMessage("Status: MEDIUM\r\n");
    HAL_GPIO_WritePin(LED_GPIO_Port, LED_Pin, GPIO_PIN_RESET);
}
else
{
    sendMessage("Status: HIGH - OVERFLOW ALERT!\r\n");

    HAL_GPIO_WritePin(LED_GPIO_Port, LED_Pin, GPIO_PIN_SET);
    HAL_Delay(500);

    HAL_GPIO_WritePin(LED_GPIO_Port, LED_Pin, GPIO_PIN_RESET);
    HAL_Delay(500);
}
```

Replace `LED_GPIO_Port` and `LED_Pin` with the GPIO port and pin configured for the LED in your STM32 project. This snippet belongs inside the main program loop, after reading the ADC value and calculating the percentage.

## 8. Circuit Connections

### Potentiometer

| Potentiometer Pin | Connection |
|---|---|
| VCC | STM32 3V3 |
| GND | STM32 GND |
| SIG / Middle Pin | STM32 PA0 |

### Overflow LED

| LED Connection | Connection |
|---|---|
| Anode (+) | Configured GPIO output through a 220 Ω resistor |
| Cathode (−) | GND |

## 9. Procedure

1. Open the Wokwi online simulator.
2. Create an STM32 Nucleo-L031K6 project.
3. Add a potentiometer and a red LED with a 220 Ω resistor.
4. Connect the potentiometer to 3V3, GND, and PA0.
5. Connect the LED to the configured GPIO output and GND.
6. Configure ADC1, GPIO, UART, and the system clock in the STM32 HAL project.
7. Enter the program and compile it.
8. Start the simulation and open the Serial Monitor.
9. Rotate the potentiometer to simulate different water levels.
10. Observe the ADC value, percentage, and water-level status.
11. Increase the input above 70% and verify that the overflow LED blinks.

## 10. Expected Output

### LOW Water Level

```text
ADC Value: 800
Water Level: 19%
Status: LOW
```

### MEDIUM Water Level

```text
ADC Value: 2200
Water Level: 53%
Status: MEDIUM
```

### HIGH Water Level

```text
ADC Value: 3500
Water Level: 85%
Status: HIGH - OVERFLOW ALERT!
```

When the water level exceeds 70%, the red LED blinks to provide a visual warning.

## 11. Working

The potentiometer produces an analog voltage corresponding to the simulated water level. The STM32 ADC converts this voltage into a digital value between 0 and 4095.

The controller calculates the water-level percentage and compares it with the defined thresholds. The result is displayed on the Serial Monitor through UART. When the water level exceeds 70%, the controller activates the overflow warning by blinking the red LED.

## 12. Result

The design of the water level indicator using the STM32 Nucleo-L031K6 was described. The system classifies the input as LOW, MEDIUM, or HIGH and provides a blinking LED warning for a high water level when the firmware and hardware are configured correctly.
**OUTPUT**
<img width="1535" height="774" alt="image" src="https://github.com/user-attachments/assets/30667b89-dd42-4c0c-ab38-2ebb285b3783" />

## 13. Applications

- Water storage tanks
- Overhead tank monitoring
- Industrial liquid-level monitoring
- Automatic water pump control systems
- Water overflow warning systems

## 14. Future Enhancements

- Use an actual water-level sensor instead of a potentiometer.
- Add a buzzer for audible overflow alerts.
- Display the water level on an LCD or OLED.
- Automatically switch a water pump on and off.
- Integrate IoT monitoring and mobile notifications.
