# XRC Rover – ROS 2 Control Software

Software stack for a **6-wheeled rover with a robotic arm**, built as a team project at **EPFL Xplore**. It runs on **ROS 2 Humble** inside Docker and covers the whole chain: gamepad input, state machine, kinematics, serial communication with the microcontrollers, computer vision and localization.

# My role:
 - Gamepad: integrated the gamepad with the control interface ([xrc_ui](https://github.com/C2JG/xrc_ui)), turning its inputs into wheel and arm commands.
 - Wheel kinematics: computed the speed and steering angle of each wheel from the requested direction.
 - Arm inverse kinematics: computed the joint angles needed to reach a target position.
 - URDF model: designed `rover.urdf.xacro`, which describes the rover's structure and the joints between its parts.
 - Sensor data: published ultrasonic and gyroscope readings on ROS 2 topics, so the UI or any other node can subscribe to them.

## Team

- [@C2JG](https://github.com/C2JG)
- [@AquIce](https://github.com/AquIce)
  
## Features

- **Manual driving with a gamepad**: gamepad inputs are converted into wheel and arm commands.
- **Finite state machine (FSM)** with `BOOTING`, `MANUAL`, `AUTOMATIC` and `FAIL` states, plus an emergency stop.
- **Wheel kinematics**: Ackermann-style steering on 6 wheels (speed of each wheel and angle of the 4 steering wheels).
- **2-link robotic arm**: inverse kinematics, base rotation and claw control.
- **Serial communication in C++** with the electronics boards: DC motors, servos and sensors.
- **Computer vision**:
  - camera stream over GStreamer/H.264 (navigation or autonomy mode);
  - depth estimation with **MiDaS** and object detection with **YOLOv8**, used to build a point cloud;
  - **ArUco marker** detection for localization.
- **Localization**: wheel odometry, then an **Extended Kalman Filter (EKF)** fusing wheels, IMU and vision.
- **Safety**: obstacle alerts from ultrasonic sensors.
- **Navigation**: URDF model of the rover, launch files and **Nav2** configuration.

## Architecture

```
Gamepad ──► gamepad_publisher ──► fsm_node ──► wheels_manager ──► serial_link (C++) ──► motors / servos
                        └───────► arm_manager ──────────────────────┘
Camera ──► camera_publisher ──► vision / aruco_detector ──┐
Wheels ──► odometry_manager ──────────────────────────────┼──► ekf_node ──► /odom + TF
IMU ──────────────────────────────────────────────────────┘
Ultrasonic ──► ultrasonic_node ──► FSM alert
```

## Repository structure

| Folder | Contents |
|---|---|
| `rover_commands/` | Main ROS 2 Python package: nodes, kinematics, EKF, URDF, launch files, Nav2 config |
| `serial_link/` | C++ nodes handling serial communication with the microcontrollers (DC motors, servos, sensors) |
| `custom_msg/` | Custom ROS messages and services (`MoveData`, `DCSpeed`, `MPUData`, `GamepadData`…) |
| `docker_humble_desktop/` | ROS 2 Humble Docker image and build/run scripts (Linux, Mac, Windows) |
| `docs/` | Installation guides and useful commands |

## Tech stack

ROS 2 Humble · Python · C++ · Docker · OpenCV · PyTorch (MiDaS, YOLOv8) · GStreamer · Nav2 · URDF/Xacro



## Getting started

```bash
# 1. Build and run the container
cd docker_humble_desktop
./build.sh && ./run.sh

# 2. Inside the container: build the workspace
colcon build && source install/setup.bash

# 3. Run a node or the navigation stack
ros2 run rover_commands gamepad
ros2 launch rover_commands navigation.launch.py
```


_Fork of the original team project, published here as part of my portfolio._


