# VEX Robotics Object Placement System

## Overview
This program controls a VEX robot designed to autonomously locate, approach, and place objects at a target location. The robot uses infrared sensing for target detection, sonar for distance measurement, and bump sensors for obstacle avoidance and final positioning.

## Hardware Configuration

### Sensors
- **InfraCollector** (Analog Port 7) - Infrared phototransistor for target detection
- **button1** (Digital Port 1) - Start button
- **button2** (Digital Port 2) - Secondary control button
- **SonarSensor** (Digital Port 4) - Ultrasonic distance sensor
- **left_bumper** (Digital Port 7) - Left side collision detection
- **right_bumper** (Digital Port 8) - Right side collision detection
- **LED_1** (Digital Port 11) - Status indicator LED
- **arm_sensor** (Digital Port 12) - Arm position limit switch

### Motors
- **left_motor** (Port 1) - Left drive motor with encoder (I2C_1)
- **right_motor** (Port 2) - Right drive motor with encoder (I2C_2)
- **arm_motor** (Port 3) - Object placement arm motor

## System States

The robot operates through a finite state machine with the following states:

1. **STATE1** - Target Detection
   - Scans for IR beacon using oscillating search pattern
   - Approaches target when detected
   - Transitions when within 15cm of target

2. **STATE2** - Object Placement
   - Raises arm until limit switch is triggered
   - Deploys object
   - Lowers arm to reset position

3. **STATE3** - Final Positioning
   - Backs up and rotates 180°
   - Navigates to final position using bump sensors
   - Stops when sonar detects proximity (<9cm)

4. **STATE4** - Task Complete
   - Illuminates LED to signal completion

## IR Detection Thresholds

The infrared sensor uses calibrated thresholds for different distances:
- **2m detection**: < 160
- **1m detection**: < 110
- **0.5m detection**: < 95

## Key Functions

### Movement Control
- `go_straight()` - Forward motion with differential motor compensation
- `backup()` - Reverse motion
- `turn_left()` / `turn_right()` - Rotation maneuvers
- `slow_left()` / `slow_right()` - Gentle turning adjustments
- `stop_robot()` - Halt all drive motors

### Sensor Processing
- `sensor_min()` - Samples IR sensor 4 times and returns minimum value for noise reduction

### Navigation Algorithms
- `target_detection()` - Oscillating search pattern to locate and approach IR beacon
- `final_positioning()` - Wall-following behavior using bump sensors

### Mechanical Control
- `place_object()` - Automated arm sequence for object deployment

## Operation Instructions

1. Position robot in arena with object loaded in arm
2. Press button1 to start autonomous sequence
3. Robot will:
   - Search for IR beacon target
   - Approach target location
   - Deploy object
   - Navigate to final position
   - Signal completion with LED

## Motor Speed Calibration

The motor speeds are calibrated to account for mechanical differences:
- Left motor baseline: -43 (forward) / 43 (reverse)
- Right motor baseline: 33 (forward) / -33 (reverse)

These asymmetric values compensate for hardware variations to achieve straight-line motion.

## Notes

- Robot waits for button1 press before executing autonomous sequence
- IR sensor readings are filtered using minimum value from 4 samples to reduce noise
- Bump sensor responses include backup and correction maneuvers
- Final positioning uses adaptive wall-following until target distance achieved
