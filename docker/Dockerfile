#FROM robotis/ros:jazzy-ros-base-librealsense AS librealsense

# Use the base ROS2 Jazzy image
FROM ros:jazzy-ros-base AS open-manipulator

#COPY --from=librealsense /opt/librealsense /usr/local/
#COPY --from=librealsense /usr/local/lib/python3.12/dist-packages/pyrealsense2 /usr/local/lib/python3.12/dist-packages/pyrealsense2
#COPY --from=librealsense /usr/src/librealsense/config/99-realsense-libusb.rules /etc/udev/rules.d/
#COPY --from=librealsense /usr/src/librealsense/config/99-realsense-d4xx-mipi-dfu.rules /etc/udev/rules.d/
ENV PYTHONPATH=$PYTHONPATH:/usr/local/lib

# Set environment variables
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y curl gnupg lsb-release && \
    curl -fsSL https://packages.osrfoundation.org/gazebo.key | gpg --dearmor -o /usr/share/keyrings/gazebo-archive-keyring.gpg && \
    echo "deb [signed-by=/usr/share/keyrings/gazebo-archive-keyring.gpg] https://packages.osrfoundation.org/gazebo/ubuntu-stable $(lsb_release -cs) main" \
    | tee /etc/apt/sources.list.d/gazebo-stable.list > /dev/null && \
    apt-get update && \
    apt-get install -y gz-harmonic && \
    rm -rf /var/lib/apt/lists/*


# Install common system dependencies
RUN apt-get update && \
    apt-get install -y wget git vim nano curl && \
    apt-get install -y \
    ros-${ROS_DISTRO}-hardware-interface \
    ros-${ROS_DISTRO}-controller-manager \
    ros-${ROS_DISTRO}-ros2-controllers \
    ros-${ROS_DISTRO}-tf-transformations \
    ros-${ROS_DISTRO}-ros2-control-cmake \
    ros-${ROS_DISTRO}-gz* \
    ros-${ROS_DISTRO}-pal-statistics &&\
    apt-get install -y ros-${ROS_DISTRO}-moveit-* --no-install-recommends &&\
    rm -rf /var/lib/apt/lists/*




# Install realsense dependencies
#RUN apt-get update && apt-get install -y \
  #  libusb-1.0-0-dev \
  #  libglfw3-dev \
  #  ros-${ROS_DISTRO}-image-transport-plugins \
  #  vim \
  #  nano \
  #  && rm -rf /var/lib/apt/lists/*

ENV COLCON_WS=/root/ros2_ws
WORKDIR ${COLCON_WS}


RUN mkdir -p ${COLCON_WS}/src && \
    cd ${COLCON_WS}/src && \
