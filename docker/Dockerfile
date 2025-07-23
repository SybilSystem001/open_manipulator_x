#FROM robotis/ros:jazzy-ros-base-librealsense AS librealsense

# Use the base ROS2 Jazzy image
FROM ros:jazzy-ros-base AS open-manipulator

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


# Install common system dependencies, depthai && gazebo dependencies 
RUN apt-get update && \
    apt-get install -y wget git vim nano curl && \
    apt-get install -y \
    ros-${ROS_DISTRO}-hardware-interface \
    ros-${ROS_DISTRO}-controller-manager \
    ros-${ROS_DISTRO}-ros2-controllers \
    ros-${ROS_DISTRO}-depthai-ros \
    ros-${ROS_DISTRO}-cv-bridge \
    ros-${ROS_DISTRO}-image-transport python3-opencv \
    ros-${ROS_DISTRO}-rqt-image-view \
    ros-${ROS_DISTRO}-nav2-bringup \
    ros-${ROS_DISTRO}-vision-msgs \
    ros-${ROS_DISTRO}-image-transport \
    ros-${ROS_DISTRO}-tf-transformations \
    ros-${ROS_DISTRO}-ros2-control-cmake \
    ros-${ROS_DISTRO}-gz* \
    ros-${ROS_DISTRO}-pal-statistics &&\
    apt-get install -y ros-${ROS_DISTRO}-moveit-* --no-install-recommends &&\
    rm -rf /var/lib/apt/lists/*

ENV COLCON_WS=/root/ros2_ws
WORKDIR ${COLCON_WS}

RUN mkdir -p ${COLCON_WS}/src && \
    cd ${COLCON_WS}/src && \
    git clone -b jazzy https://github.com/ROBOTIS-GIT/dynamixel_hardware_interface.git && \
    git clone -b jazzy https://github.com/ROBOTIS-GIT/dynamixel_interfaces.git && \
    git clone -b jazzy https://github.com/ROBOTIS-GIT/DynamixelSDK.git && \
    git clone -b jazzy https://github.com/SybilSystem001/Moveit_open_x.git && \
    git clone -b jazzy https://github.com/SybilSystem001/TB4_jazzy_obj_det.git && \
    # git clone -b jazzy https://github.com/ROBOTIS-GIT/open_manipulator.git && \
    git clone -b jazzy https://github.com/SybilSystem001/open_manipulator_x.git

RUN cd ${COLCON_WS} && \
    apt-get update && \
    rosdep update && \
    rosdep install -i --from-path src --rosdistro $ROS_DISTRO --skip-keys="dynamixel_hardware_interface dynamixel_interfaces dynamixel_sdk open_manipulator" -y && \
    rm -rf /var/lib/apt/lists/*

RUN bash -c "source /opt/ros/${ROS_DISTRO}/setup.bash && \
    cd ${COLCON_WS} && \
    colcon build --symlink-install --executor sequential --cmake-args -DCMAKE_BUILD_TYPE=Release"

RUN echo "source /opt/ros/${ROS_DISTRO}/setup.bash" >> ~/.bashrc  && \
    echo "source ${COLCON_WS}/install/setup.bash" >> ~/.bashrc  && \
    echo "alias cb='colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release'" >> ~/.bashrc

CMD ["bash"]



