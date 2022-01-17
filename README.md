# notes-fmyslef

#ROS 2 Cyclone DDS zenith-ros2-dds plugin

First install ROS2 foxy from steps below
https://docs.ros.org/en/foxy/Installation/Ubuntu-Install-Binary.html

Create a Workspace
https://docs.ros.org/en/foxy/Tutorials/Workspace/Creating-A-Workspace.html


#Install the  zenith-ros2-dds plugin
https://github.com/eclipse-zenoh/zenoh-plugin-dds

``cd ~/ros_ws/src/``

``git clone https://github.com/eclipse-zenoh/zenoh-plugin-dds.git``

``cd ~/ros_ws``

``rosdep install -q --from-paths src --ignore-src -y``

``colcon build --cmake-args -DCMAKE_BUILD_TYPE=Release``

``source setup.bash``

#Demo

``ROS_DOMAIN_ID=1 ros2 run turtlesim turtlesim_node``

``ROS_DOMAIN_ID=2 ros2 run turtlesim turtle_teleop_key``

``./target/release/zenoh-bridge-dds -d 1``

``./target/release/zenoh-bridge-dds -d 2``





NOTES
start the plugin with
``ros2 run zenoh_bridge_dds zenoh_bridge_dds -d 1``


