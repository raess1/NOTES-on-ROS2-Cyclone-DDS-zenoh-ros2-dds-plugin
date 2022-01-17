# ROS2 Cyclone DDS + zenith-ros2-dds plugin

This is my notes on testing ROS2 Cyclone DDS + zenith-ros2-dds plugin. In this setup I am running two ubuntu 20.04 VM´s on Hyper-V on two diffrent computers. 

So lets get started.  
First we need to create a new hyper-v virtual switch from the VM that will act as a "host" This is becuase we wnt the ability to forward ports on it.  
How to add a new hyper-v virtual switch:   
https://docs.microsoft.com/en-us/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines

Let's continue preparations, In this setup we will target the PORT: 7447/UDP

We want to open the port. In my case I am using Google wifi. And this is how I do it.  
- Open the Google Home app .  
- Tap Wi-Fi Settings. ...  
- Tap Port management. ...  
- Select the tab for the type of IP address you're forwarding.   
- Select a device. (My host)  
- Add your internal and external ports. (7447) (7447)  
- Choose User Datagram Protocol (UDP).  
- Tap Save .  
  
**Port 7447 is now public open.**

# On the host in (ubuntu)  
``sudo apt-get install ufw``    
``sudo ufw enable``  
``sudo ufw allow 7447/udp``  
check status ``sudo ufw status verbose``  


# Let's continue installing ROS2 

First install ROS2 foxy from steps below  
https://docs.ros.org/en/foxy/Installation/Ubuntu-Install-Binary.html

Create a Workspace  
https://docs.ros.org/en/foxy/Tutorials/Workspace/Creating-A-Workspace.html

#Working with Eclipse Cyclone DDS (dont forget ``export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp``)  
https://docs.ros.org/en/foxy/Installation/DDS-Implementations/Working-with-Eclipse-CycloneDDS.html

#Install the  zenith-ros2-dds plugin  
https://github.com/eclipse-zenoh/zenoh-plugin-dds

``cd ~/ros_ws/src/``  
``git clone https://github.com/eclipse-zenoh/zenoh-plugin-dds.git``  
``cd ~/ros_ws``  
``. ~/ros2_foxy/ros2-linux/setup.bash``  
``. install/local_setup.bash``  
``rosdep install -q --from-paths src --ignore-src -y``  
``colcon build --cmake-args -DCMAKE_BUILD_TYPE=Release``    
``. install/local_setup.bash``  




# Create an cyclonedds.xml with settings

``
https://github.com/raess1/NOTES-on-ROS2-Cyclone-DDS-zenith-ros2-dds-plugin/blob/main/cyclonedds.xml
``

``. ~/ros2_foxy/ros2-linux/setup.bash``  
``. install/local_setup.bash``  
``export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp``  
``export CYCLONEDDS_URI=file://YOURPATH/cyclonedds.xml``  


# Explicitly activate multicast on loopback on Unix systems doing:  
``sudo route add -net 224.0.0.0 netmask 240.0.0.0 dev lo``  
``sudo ifconfig lo multicast``  

# On k3lso quadruped run  
``ros2 run zenoh_bridge_dds zenoh_bridge_dds --no-multicast-scouting -l udp/0.0.0.0:7447 -e udp/192.168.86.37:7447``  
# In the base station  
``ros2 run zenoh_bridge_dds zenoh_bridge_dds --no-multicast-scouting -l udp/0.0.0.0:7447``  


# Pace the traffic (documents is in work)   

**Control the list of topics that are allowed to be routed over zenoh**  
``--allow``  

**Specify a maximum frequency of data routing over zenoh per-topic**  
``--max-frequency <String>`` 
