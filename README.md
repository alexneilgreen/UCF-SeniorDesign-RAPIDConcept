<!-- SHOWCASE: false -->

# RAPID - Robotic Autonomous Platform for Item Delivery

> A ROS2-based autonomous delivery robot using LiDAR, computer vision, RFID verification, and custom PCBs to navigate and securely deliver items within a workspace.

> [!WARNING]
> This is an archived, incomplete iteration of the RAPID software stack. It does not represent the final working implementation. For the completed version, see [RAPID-Deployed](https://github.com/alexneilgreen/RAPID-Deployed).

![Status](https://img.shields.io/badge/status-archived-lightgrey)
![Language](https://img.shields.io/badge/language-Python%20%7C%20C%2B%2B%20%7C%20XML%20%7C%20XACRO%20%7C%20YAML-blue)
![Semester](https://img.shields.io/badge/semester-Fall%202023%20%2F%20Spring%202024-orange)

---

## Course Information

| Field                  | Details                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Course Title           | Senior Design I / Senior Design II                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| Course Number          | EEL 4914 / EEL 4915                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Semester               | Fall 2023 / Spring 2024                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Assignment Title       | RAPID - Robotic Autonomous Platform for Item Delivery                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Assignment Description | Design, document, and build a fully functional, professional-grade engineering system transitioning from a conceptual research phase (Senior Design I) to a physical prototype (Senior Design II). Requirements included a custom PCB performing a critical system function, embedded software developed to industry standards (ISO/IEC or IEEE), a durable prototype capable of live demonstration to industry judges, and a comprehensive design report (~120+ pages). |

---

## Project Description

RAPID is an autonomous robot designed to relieve workers of manual delivery tasks within a professional workspace. The system integrates a ROS2 Humble Hawksbill software stack running across a dual Raspberry Pi 4B cluster, using SLAM and NAV2 for autonomous navigation, OpenCV blob detection for obstacle avoidance, and an RC522-based RFID verification system to ensure secure package handoff. Hardware is anchored by two custom-manufactured PCBs: a multi-revision power distribution board (Altium Designer, JLCPCB) and an ATmega2560 motor controller board with an ESP32-S3 backup variant (EasyEDA, JLCPCB). This repository represents the archived first iteration of the ROS2 package used during development; a separate, completed repository reflects the final production implementation.

---

## Screenshots / Demo

## Available on [RAPID-Deployed](https://github.com/alexneilgreen/RAPID-Deployed).

## Results

When the full software stack is running correctly, the expected behavior is as follows:

1. The GUI launches and prompts the operator to load the robot and select a delivery destination.
2. SLAM builds a live occupancy map visible in RViz; the robot navigates autonomously via NAV2 while OpenCV monitors camera feeds for small obstacles below the LiDAR's detection threshold.
3. Upon arrival, the GUI transitions to an RFID prompt. Scanning the correct card disengages the solenoid lock; an incorrect scan prints an error and continues prompting.
4. Once the item is retrieved and confirmed via the GUI, the lock re-engages and the system returns to idle.

**Sample terminal output when launching the simulation:**

```bash
# Terminal 1 - Robot State Publisher + Gazebo
ros2 launch my_bot launch_sim.launch.py world:=./src/my_bot/worlds/obstacles.world

# Terminal 2 - SLAM (simulation mode)
ros2 launch slam_toolbox online_async_launch.py \
  slam_params_file:=./src/my_bot/config/mapper_params_online_async.yaml \
  use_sim_time:=true

# Terminal 3 - Teleop (manual test)
ros2 run teleop_twist_keyboard teleop_twist_keyboard \
  --ros-args -r /cmd_vel:=diff_cont/cmd_vel_unstamped
```

**What to look for:** RViz should display the robot model, an expanding occupancy grid map from the LiDAR, and the robot's odometry frame (`odom`). If the map is not populating, verify the `slam_params_file` path and confirm `use_sim_time` matches the launch configuration. If the robot does not move, confirm both the `diff_cont` and `joint_broad` controller spawners are active.

---

## Key Concepts

`ROS2 Humble` `SLAM` `NAV2` `URDF / XACRO` `Gazebo Simulation` `ros2_control` `OpenCV Blob Detection` `cv_bridge` `RFID Verification` `Custom PCB Design` `Tank Drive` `Differential Drive` `PWM Motor Control` `LiDAR`

---

## Languages & Tools

- **Languages:** Python, XML, XACRO, YAML, C++ (C99/C14 via CMake)
- **Framework / SDK:** ROS2 Humble Hawksbill, OpenCV, appJar, NAV2, slam_toolbox, ros2_control
- **Hardware:** Raspberry Pi 4B (x2, Ubuntu MATE), RPLiDAR A1M8-RG, Raspberry Pi Camera V2 (x2), ATmega2560 MCU (custom PCB), ESP32-S3-WROOM1 MCU (backup PCB), RC522 RFID reader, DFRobot 251 RPM DC gear motors (x4), DRI0041 dual-channel motor drivers (x2), XZNY 12V 18Ah LiFePO4 battery
- **PCB Design Tools:** Altium Designer (PDB rev. B/C), EAGLE (PDB rev. A), EasyEDA (MCU motor controller board)
- **PCB Manufacturer:** JLCPCB
- **Build System:** CMake (ament_cmake), colcon

---

## File Structure

```
my_bot/
├── CMakeLists.txt              # ROS2 ament_cmake build configuration
├── package.xml                 # ROS2 package manifest and dependencies
├── config/
│   ├── drive_bot.rviz          # RViz configuration for driving/navigation view
│   ├── view_bot.rviz           # RViz configuration for robot model inspection
│   ├── empty.yaml              # Placeholder configuration file
│   ├── gazebo_params.yaml      # Gazebo physics and plugin parameters
│   ├── mapper_params_online_async.yaml  # SLAM toolbox tuning parameters
│   └── my_controllers.yaml     # ros2_control controller definitions (skid-steer diff drive)
├── description/
│   ├── robot.urdf.xacro        # Top-level URDF entry point; assembles all xacro includes
│   ├── robot_core.xacro        # Base chassis links, joints, and wheel geometry
│   ├── inertial_macros.xacro   # Reusable inertia tensor macros for rigid bodies
│   ├── ros2_control.xacro      # ros2_control hardware interface definitions
│   ├── gazebo_control.xacro    # Gazebo differential drive plugin configuration
│   ├── lidar.xacro             # RPLiDAR sensor link and joint definition
│   ├── front_camera.xacro      # Front Raspberry Pi Camera V2 link definition
│   └── rear_camera.xacro       # Rear Raspberry Pi Camera V2 link definition
├── launch/
│   ├── rsp.launch.py           # Robot State Publisher launch (URDF broadcast)
│   ├── launch_sim.launch.py    # Full simulation launch (RSP + Gazebo + controllers)
│   ├── single_camera.launch.py # Launch file for a single v4l2 camera node
│   └── dual_camera.launch.py   # Launch file for dual v4l2 camera nodes
├── worlds/
│   ├── empty.world             # Flat Gazebo world for baseline testing
│   ├── obstacles.world         # Gazebo world with obstacle objects for navigation testing
│   └── SLAM.world              # Gazebo world configured for SLAM mapping tests
├── SystemInfo.txt              # Developer setup notes: ROS2 install, network config, drivers
└── FileOrgGenerator.py         # Utility script to generate recursive directory listings
```

---

## Installation & Usage

### Prerequisites

- Ubuntu MATE 22.04 (on Raspberry Pi 4B or development machine)
- ROS2 Humble Hawksbill
- colcon build system
- Python 3 with `python3-colcon-common-extensions`

### Setup

```bash
# 1. Install ROS2 Humble (if not already installed)
sudo apt update && sudo apt install curl gnupg2 lsb-release
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key \
  -o /usr/share/keyrings/ros-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) \
  signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] \
  http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" | \
  sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
sudo apt update && sudo apt install ros-humble-desktop
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
source ~/.bashrc

# 2. Install required ROS2 packages
sudo apt install ros-humble-gazebo-ros-pkgs \
                 ros-humble-xacro \
                 ros-humble-joint-state-publisher-gui \
                 ros-humble-slam-toolbox \
                 ros-humble-navigation2 \
                 ros-humble-nav2-bringup \
                 ros-humble-rplidar-ros \
                 ros-humble-v4l2-camera \
                 ros-humble-image-transport-plugins \
                 python3-colcon-common-extensions \
                 python3-serial

# 3. Create a workspace and clone the repository
mkdir -p ~/dev_ws/src && cd ~/dev_ws/src
git clone https://github.com/alexneilgreen/UCF-SeniorDesign-RAPID.git my_bot
cd ~/dev_ws

# 4. Build the package
colcon build --symlink-install
source install/setup.bash

# 5. Launch the Gazebo simulation
ros2 launch my_bot launch_sim.launch.py world:=./src/my_bot/worlds/obstacles.world
```

### Running SLAM and Navigation (Simulation)

```bash
# Terminal 1 - already running from step 5 above

# Terminal 2 - Start SLAM toolbox
ros2 launch slam_toolbox online_async_launch.py \
  slam_params_file:=./src/my_bot/config/mapper_params_online_async.yaml \
  use_sim_time:=true

# Terminal 3 - Manual keyboard control (for testing)
ros2 run teleop_twist_keyboard teleop_twist_keyboard \
  --ros-args -r /cmd_vel:=diff_cont/cmd_vel_unstamped
```

### Controls

| Key / Command | Action                                 |
| ------------- | -------------------------------------- |
| `i`           | Drive forward                          |
| `,`           | Drive backward                         |
| `j`           | Turn left                              |
| `l`           | Turn right                             |
| `k`           | Stop                                   |
| `q` / `z`     | Increase / decrease max speed          |
| `w` / `x`     | Increase / decrease linear speed only  |
| `e` / `c`     | Increase / decrease angular speed only |

---

## Contributors

| Name               | Primary Responsibilities                                  | Secondary Responsibilities                              | GitHub                                             |
| ------------------ | --------------------------------------------------------- | ------------------------------------------------------- | -------------------------------------------------- |
| Alexander Green    | Software stack development, LiDAR software, 3D modeling   | Computer vision, GUI                                    | [@alexneilgreen](https://github.com/alexneilgreen) |
| Brandon Holtzman   | Computer vision, GUI                                      | Software stack development, LiDAR software, 3D modeling | [@Example1](https://github.com/Example1)           |
| Antonio Duchesneau | Microcontroller PCB design, driving subsystem             | Power PCB design, verification subsystem                | [@Example1](https://github.com/Example1)           |
| Arthur Radulescu   | Power PCB design, RFID PCB design, verification subsystem | Microcontroller PCB design, driving subsystem           | [@Example1](https://github.com/Example1)           |

---

## Academic Integrity

This repository is publicly available for **portfolio and reference purposes only**.
Please do not submit any part of this work as your own for academic coursework.
