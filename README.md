# DCMotorTest

A small project for testing and exercising DC motors using a microcontroller and a motor driver. This repository contains code, wiring notes, and test procedures to help you validate motor functionality, measure performance, and debug common issues.

## Features

- Basic motor on/off and PWM speed control
- Direction control (if using an H-bridge driver)
- Simple test routines for verifying motor behavior

## Hardware

Typical components used with this project:

- Microcontroller (Arduino Uno / Nano / ESP32 / similar)
- DC motor
- Motor driver (L298N, TB6612, L293D, or BTS7960 etc.)
- External power supply for motor (voltage/current as required by motor)
- Connecting wires, breadboard or PCB

## Wiring (example)

- Motor driver VCC -> Motor power supply
- Motor driver GND -> Common ground with microcontroller
- Motor driver IN1/IN2 -> Digital pins on microcontroller (direction)
- Motor driver ENA (or PWM pin) -> PWM-capable pin on microcontroller

Adjust wiring according to your specific driver module. Always ensure the motor power supply can provide adequate current and that grounds are common.

## Usage

1. Connect the hardware as described above.
2. Upload the microcontroller sketch/examples to your board.
3. Open a serial monitor (if the sketch uses serial output) or use the on-board controls to run tests.
4. Observe motor behavior, measure current draw, and verify speed and direction control.

## Example Arduino sketch (simple PWM test)

```cpp
const int pwmPin = 9;    // PWM pin
const int dirPin = 8;    // Direction pin

void setup() {
  pinMode(pwmPin, OUTPUT);
  pinMode(dirPin, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  // Rotate forward at increasing speeds
  digitalWrite(dirPin, HIGH);
  for (int speed = 0; speed <= 255; speed += 25) {
    analogWrite(pwmPin, speed);
    delay(500);
  }

  // Stop
  analogWrite(pwmPin, 0);
  delay(1000);

  // Rotate reverse at decreasing speeds
  digitalWrite(dirPin, LOW);
  for (int speed = 255; speed >= 0; speed -= 25) {
    analogWrite(pwmPin, speed);
    delay(500);
  }

  analogWrite(pwmPin, 0);
  delay(2000);
}
```

## Tests

- Verify motor spins in both directions
- Verify PWM speed control is linear-ish across duty cycle range
- Measure motor current at different loads

## Safety & Tips

- Never power motors from the microcontroller's 5V regulator.
- Use appropriate flyback diodes if your driver requires them.
- Start with low voltages and no load when first testing.

## Contributing

Contributions, bug reports, and improvements are welcome. Please open an issue or submit a pull request.

## License

This project is provided under the MIT License. See the LICENSE file for details.