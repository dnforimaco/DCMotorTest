# DFRobot Raspberry Pi Motor Driving Expansion Board Library

A Python library for controlling the DFRobot Raspberry Pi Motor Driving Expansion Board, providing easy-to-use interfaces for DC motors, stepper motors, and servo motors.

## Features

- **DC Motor Control**: Control up to 4 DC motors with variable speed and direction
- **Stepper Motor Control**: Support for 2 stepper motors with multiple stepping modes
- **Servo Motor Control**: Control up to 8 servo motors with precise angle positioning
- **I2C Communication**: Uses I2C protocol for reliable communication with the motor HAT
- **PWM Control**: 16-channel PWM controller for precise motor control

## Hardware Requirements

- Raspberry Pi (any model with I2C support)
- DFRobot Raspberry Pi Motor Driving Expansion Board
- DC motors, stepper motors, and/or servo motors as needed

## Software Requirements

- Python 3.x
- smbus library (`sudo apt-get install python3-smbus`)
- RPi.GPIO library (if not already installed)

## Installation

1. Clone or download this repository to your Raspberry Pi
2. Ensure I2C is enabled on your Raspberry Pi:
   ```bash
   sudo raspi-config
   # Navigate to Interfacing Options > I2C > Enable
   ```

3. Install required dependencies:
   ```bash
   sudo apt-get update
   sudo apt-get install python3-smbus python3-rpi.gpio
   ```

## Usage

### Basic Setup

```python
from Emakefun_MotorHAT import Emakefun_MotorHAT

# Create motor HAT object
mh = Emakefun_MotorHAT(addr=0x60)  # Default I2C address
```

### DC Motor Control

```python
# Get motor object (motors numbered 1-4)
myMotor = mh.getMotor(1)

# Set speed (0-255)
myMotor.setSpeed(150)

# Run motor
myMotor.run(Emakefun_MotorHAT.FORWARD)   # Forward
myMotor.run(Emakefun_MotorHAT.BACKWARD)  # Backward
myMotor.run(Emakefun_MotorHAT.RELEASE)   # Stop
myMotor.run(Emakefun_MotorHAT.BRAKE)     # Brake
```

### Stepper Motor Control

```python
# Get stepper motor object (steppers numbered 1-2)
myStepper = mh.getStepper(200, 1)  # 200 steps per revolution

# Set speed in RPM
myStepper.setSpeed(30)

# Step motor
myStepper.step(100, Emakefun_MotorHAT.FORWARD, Emakefun_MotorHAT.SINGLE)
myStepper.step(100, Emakefun_MotorHAT.BACKWARD, Emakefun_MotorHAT.DOUBLE)
```

#### Stepping Modes

- `SINGLE`: Single coil activation
- `DOUBLE`: Dual coil activation (more torque)
- `INTERLEAVE`: Half-stepping for smoother motion
- `MICROSTEP`: Microstepping for finest control

### Servo Motor Control

```python
# Get servo object (servos numbered 1-8)
myServo = mh.getServo(1)

# Set angle (0-180 degrees)
myServo.writeServo(90)  # Move to 90 degrees

# Set angle with speed control (1-10, 10 is fastest)
myServo.writeServoWithSpeed(180, 5)
```

### Complete Example

```python
#!/usr/bin/python
from Emakefun_MotorHAT import Emakefun_MotorHAT, Emakefun_DCMotor
import time
import atexit

# Create motor HAT object
mh = Emakefun_MotorHAT(addr=0x60)

# Auto-disable motors on shutdown
def turnOffMotors():
    mh.getMotor(1).run(Emakefun_MotorHAT.RELEASE)
    mh.getMotor(2).run(Emakefun_MotorHAT.RELEASE)
    mh.getMotor(3).run(Emakefun_MotorHAT.RELEASE)
    mh.getMotor(4).run(Emakefun_MotorHAT.RELEASE)

atexit.register(turnOffMotors)

# DC Motor example
myMotor = mh.getMotor(1)
myMotor.setSpeed(150)
myMotor.run(Emakefun_MotorHAT.FORWARD)

# Servo example
myServo = mh.getServo(1)
myServo.writeServo(90)

# Stepper example
myStepper = mh.getStepper(200, 1)
myStepper.setSpeed(30)
myStepper.step(100, Emakefun_MotorHAT.FORWARD, Emakefun_MotorHAT.DOUBLE)
```

## API Reference

### Emakefun_MotorHAT Class

Main controller class for the motor HAT.

#### Constructor
- `Emakefun_MotorHAT(addr=0x60, freq=50)`: Create motor HAT object
  - `addr`: I2C address (default: 0x60)
  - `freq`: PWM frequency in Hz (default: 50)

#### Methods
- `getMotor(num)`: Get DC motor object (1-4)
- `getStepper(steps, num)`: Get stepper motor object (1-2)
- `getServo(num)`: Get servo motor object (1-8)

### Emakefun_DCMotor Class

DC motor control class.

#### Methods
- `setSpeed(speed)`: Set motor speed (0-255)
- `run(command)`: Control motor direction
  - `FORWARD`: Forward direction
  - `BACKWARD`: Backward direction
  - `RELEASE`: Stop motor (coast)
  - `BRAKE`: Stop motor (brake)

### Emakefun_StepperMotor Class

Stepper motor control class.

#### Constructor
- `Emakefun_StepperMotor(controller, num, steps=200)`: Create stepper motor object
  - `steps`: Steps per revolution (default: 200)

#### Methods
- `setSpeed(rpm)`: Set speed in RPM
- `step(steps, direction, style)`: Step the motor
  - `steps`: Number of steps
  - `direction`: FORWARD or BACKWARD
  - `style`: SINGLE, DOUBLE, INTERLEAVE, or MICROSTEP

### Emakefun_Servo Class

Servo motor control class.

#### Methods
- `writeServo(angle)`: Set servo angle (0-180 degrees)
- `writeServoWithSpeed(angle, speed)`: Set servo angle with speed control
  - `speed`: Speed (1-10, 10 is fastest)
- `readDegrees()`: Get current servo angle

## Testing

Run the included test script to verify your setup:

```bash
python3 DCTest.py
```

This will test DC motor functionality with speed ramping in both directions.

## Troubleshooting

### I2C Issues
- Ensure I2C is enabled in `raspi-config`
- Check I2C address: `sudo i2cdetect -y 1`
- Verify motor HAT is properly connected

### Motor Not Moving
- Check power supply to motors
- Verify motor wiring
- Ensure correct motor number (1-4 for DC, 1-2 for stepper)
- Check motor specifications match your hardware

### Permission Issues
- Run scripts with sudo if I2C access is denied
- Add user to i2c group: `sudo usermod -a -G i2c $USER`

## File Structure

- `Emakefun_MotorHAT.py`: Main library file with motor control classes
- `Emakefun_MotorDriver.py`: PWM controller using PCA9685
- `Emakefun_I2C.py`: I2C communication utilities
- `DCTest.py`: Example DC motor test script

## License

This library is provided as-is for educational and hobbyist use.

## Contributing

Feel free to submit issues and pull requests for improvements.

## Support

For hardware-specific questions, refer to the DFRobot documentation for the Raspberry Pi Motor Driving Expansion Board.
