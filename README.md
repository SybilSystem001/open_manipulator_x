# OpenMANIPULATOR Custom Forge

## Overview

This repository provides an integrated management package for **ROBOTIS** robotic arm the **OpenMANIPULATOR-X**

With this integration, all Robotis manipulators are managed under a unified package to ensure better compatibility and functionality.

## Key Features

This package supports **ROS 2 Jazzy** and **Gazebo Harmonic** on Ubuntu 24.04, offering the following functionalities:

- **Integration of MoveIt 2 for Enhanced Motion Planning**
- **Graphical User Interface (GUI) Implementation and Support**

# **OpenMANIPULATOR User Guide**

## **1. Introduction**

The **OMX** is a 4-DOF robotic arm designed for advanced robotic manipulation tasks. This ROS 2 package provides seamless integration, enhanced control, and versatile functionality for simulation and hardware applications.

## **2. Installation Methods**

You can choose between two installation methods:

### **Option 1: Using Docker (Recommended)**

This method provides an isolated environment with all dependencies pre-installed.

1. **Install Docker and Docker Compose**
   Follow the official Docker installation guide: [Install Docker Engine](https://docs.docker.com/engine/install/)

2. **Clone the Repository**
   ```bash
   git clone https://github.com/SybilSystem001/open_manipulator_x.git
   cd open_manipulator_x
   ```

3. **Build Container Image**
   #Inside the folder where the dockerfile is located (Open_manipulator_x/docker) run
   ```bash
   docker build -t forge-open-manipulator .
   ```

5. **Container Management**

   The repository includes a container management script with the following commands:

   ```bash
   # Show help
   ./docker/container.sh help

   # Start container
   ./docker/container.sh start

   # Enter the running container
   ./docker/container.sh enter

   # Stop and remove the container
   ./docker/container.sh stop
   ```

   [***Note***] <u>When stopping the container, you'll be asked for confirmation as this will remove all unsaved data in the container.</u>

6. **Data Persistence**
   The container maps the following directories for data persistence:
   - `./docker/workspace:/workspace` - The workspace directory inside the docker folder is mapped to `/workspace` inside the container

   [***Important***] <u>Data Persistence Rules:
   - Data in `/workspace` inside the container is saved to `docker/workspace` on your host
   - To exit container type "exit" to keep current the container without deleting any saved data inside (Note: this is not persistant meaning it dissapears when the container is stopped)
   - Container restart (using `docker restart`) maintains all data
   - Container removal (using `container.sh stop`) will remove all data except what's in the mapped `/workspace` directory
   - Always save your work in the `/workspace` directory to ensure it persists after container removal</u>

### **Option 2: Host Installation**

Follow these steps if you prefer to install directly on your host system:

1. **Prerequisites**

   - **Supported ROS Version**
     ![ROS 2 Jazzy](https://img.shields.io/badge/ROS2-Jazzy-blue)
     This package is compatible only with **ROS 2 Jazzy**. Ensure that ROS 2 Jazzy is properly installed.

   - **USB Port Permissions**
     To enable communication with the hardware, add your user to the `dialout` group:
     ```bash
     sudo usermod -aG dialout $USER
     ```
     **A login and logout are required.**

2. **Install Luxonis Depthai ROS Wrapper**
   sudo apt install ros-jazzy-depthai-ros
   Please follow the official instructions for installing and using the RealSense ROS wrapper at:
   - https://github.com/IntelRealSense/realsense-ros

   This will ensure you have the latest and most compatible version for your system and camera.

3. **Clone the Repository**
   ```bash
   cd ~/ros2_ws/src
   git clone -b jazzy https://github.com/ROBOTIS-GIT/DynamixelSDK.git && \
   git clone -b jazzy https://github.com/ROBOTIS-GIT/dynamixel_interfaces.git && \
   git clone -b jazzy https://github.com/ROBOTIS-GIT/dynamixel_hardware_interface.git && \
   git clone -b jazzy https://github.com/SybilSystem001/open_manipulator_x.git && \
   git clone -b jazzy https://github.com/SybilSystem001/Moveit_open_x.git \
   ```

4. **Install ROS 2 Dependencies**
   ```bash
   cd ~/ros2_ws
   rosdep update
   rosdep install -i --from-path src --rosdistro $ROS_DISTRO --skip-keys="librealsense2 dynamixel_hardware_interface dynamixel_interfaces dynamixel_sdk open_manipulator" -y
   ```

5. **Build the Package**
   ```bash
   colcon build --symlink-install --executor sequential --cmake-args -DCMAKE_BUILD_TYPE=Release
   ```

6. **Source the Workspace**
   ```bash
   source ~/ros2_ws/install/setup.bash
   ```

7. **(Optional) Add Convenience Alias**
   Add the following to your `~/.bashrc` for a convenient build alias:
   ```bash
   echo "alias cb='colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release'" >> ~/.bashrc
   source ~/.bashrc
   ```

8. **Set environment variables to conect to the turtlebot 4 (Runing ROS@ Jazzy)**
   # open the script located in the folder, with the same environment variables as the TB4 current values:
    Note: the turtlebot for is working with the namespace = /tb4_jazzy
  - Ip address: 192.168.0.1# (this value is on the turtlebot screen)
  - ros domain id:16
  - discovery server id: 16
  - create3 port:8080
   ```bash
   ./configure_discovery.sh
   ```
   #In order to save changes execute:
   ```bash
   source ~/.bashrc
   ```
   #Restart the daemon
   ```bash
   ros2 daemon stop; ros2 daemon start
   ```
   #Check the topic list to see if they are connected
   ```bash
   ros2 topic list
   ```

10. **Create and apply udev rules**
   ```bash
   ros2 run open_manipulator_bringup om_create_udev_rules
   ```

## **3. Launch Files Overview**

Below is a comprehensive list of available launch files, grouped by function. Use these to start the system in various modes, simulations, or with special features.

### **A. Hardware Launch (Real Robot)**

```bash
# Launch OpenMANIPULATOR-X hardware
ros2 launch open_manipulator_bringup omx.launch.py
#launch in case of OpenCR
ros2 launch open_manipulator_bringup omx.launch.py port_name:=/dev/ttyACM0

```

### **B. Simulation (Gazebo)**

```bash

# Simulate OpenMANIPULATOR-X in Gazebo
ros2 launch open_manipulator_bringup omx_gazebo.launch.py

```


### **C. GUI Launch**

```bash
# GUI for OpenMANIPULATOR-X
ros2 launch open_manipulator_gui omx_gui.launch.py
```

### **D. MoveIt! Launch**

```bash
# MoveIt! for OpenMANIPULATOR-X
ros2 launch open_manipulator_moveit_config omx_moveit.launch.py
# Moveit pick and place script (Run it simultaneusly in a separate terminal)
ros2 run hello_moveit hello_moveit
```

### **E. Description Launch**

```bash

# Load robot description for OpenMANIPULATOR-X
ros2 launch open_manipulator_description omx.launch.py
```

---

### **Mode Clarification**

- **Hardware Mode:** For real robot operation, use the hardware launch files from section A.
- **Simulation Mode:** For Gazebo simulation, use the launch files from section B.

## (Legacy) ROBOTIS e-Manual for OpenMANIPULATOR-X

- [ROBOTIS e-Manual](https://emanual.robotis.com/docs/en/platform/openmanipulator_x/overview/)

The OpenMANIPULATOR-X  and the e-Manual is currently being updated.
