# Based on https://robotics.stackexchange.com/questions/97296/building-ros1-bridge-fails-in-docker-container-noetic-foxy
# with apt key fix from https://github.com/osrf/docker_images/issues/607#issuecomment-1058734931
FROM osrf/ros:foxy-ros1-bridge

RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654 \
  && apt-get update \
  && apt-get install -y ssh \
    build-essential \
    gcc \
    g++ \
    gdb \
    clang \
    cmake \
    rsync \
    nano \
    tar \
    python3 \
    ros-foxy-launch* \
  && apt-get clean

RUN apt-get install -y ros-noetic-rosauth ros-noetic-rosbridge-suite

WORKDIR "/home/workspace/"
RUN colcon build --symlink-install --packages-skip ros1_bridge

# build AGRARSENSE_ROS_Messages
RUN git clone https://AMKFrostBit@dev.azure.com/AMKFrostBit/AGRARSENSE/_git/AGRARSENSE_ROS_Messages src/AGRARSENSE_ROS_Messages

WORKDIR "/home/workspace/src/AGRARSENSE_ROS_Messages/ros1ws/"
RUN . /opt/ros/noetic/setup.sh \
  && catkin_make

WORKDIR "/home/workspace/src/AGRARSENSE_ROS_Messages/ros2ws/"
RUN . /opt/ros/foxy/setup.sh \
  && colcon build

# build ros1_bridge (including AGRARSENSE_ROS_Messages)
WORKDIR "/home/workspace/"
RUN git clone --single-branch --branch foxy https://github.com/ros2/ros1_bridge src/ros1_bridge

WORKDIR "/home/workspace/src/ros1_bridge"
RUN . /opt/ros/noetic/setup.sh \
  && . /opt/ros/foxy/setup.sh \
  && . /home/workspace/src/AGRARSENSE_ROS_Messages/ros1ws/devel/setup.sh \
  && . /home/workspace/src/AGRARSENSE_ROS_Messages/ros2ws/install/setup.sh \
  && colcon build --symlink-install --packages-select ros1_bridge --cmake-force-configure

WORKDIR "/home/workspace"

RUN echo '#!/bin/bash' > start_rosbridge_server \
  && echo 'source /opt/ros/noetic/setup.bash && roslaunch rosbridge_server rosbridge_tcp.launch bson_only_mode:=True' >> start_rosbridge_server \
  && chmod +x start_rosbridge_server

RUN echo '#!/bin/bash' > start_ros1_bridge \
  && echo 'source /opt/ros/foxy/setup.bash && ros2 run ros1_bridge dynamic_bridge --bridge-all-topics' >> start_ros1_bridge \
  && chmod +x start_ros1_bridge

CMD /bin/bash
