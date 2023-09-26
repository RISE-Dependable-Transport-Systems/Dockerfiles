Docker container to connect to [AGRARSENSE simulator](https://dev.azure.com/AMKFrostBit/AGRARSENSE) via ROS2 without requiring you to install ROS1 & Ubuntu 20.04.

Internally it runs ROSBridge (ROS1) and ROS1_Bridge (ROS1 -> ROS2) including [custom AGRARSENSE messages](https://AMKFrostBit@dev.azure.com/AMKFrostBit/AGRARSENSE/_git/AGRARSENSE_ROS_Messages).

1. Build docker container (requires docker installed, run in this directory):

        docker build -t agrarsense-ros1-bridge:latest .
   
2. Run ROSBridge server in one terminal

        docker run -it --network host agrarsense-ros1-bridge ./start_rosbridge_server

3. Run ROS1_Bridge in another terminal

       docker run -it --network host agrarsense-ros1-bridge ./start_ros1_bridge
