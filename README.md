# notes-fmyslef

#ROS 2 Cyclone DDS zenith-ros2-dds plugin

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

``rosdep install -q --from-paths src --ignore-src -y``

``colcon build --cmake-args -DCMAKE_BUILD_TYPE=Release``

``source setup.bash``


# Create an cyclonedds.xml with settings

``
<?xml version="1.0" encoding="UTF-8" ?>
<CycloneDDS xmlns="https://cdds.io/config" 
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
            xsi:schemaLocation="https://cdds.io/config https://raw.githubusercontent.com/eclipse-cyclonedds/cyclonedds/master/etc/cyclonedds.xsd">
    <Domain id="any">
        <General>
            <NetworkInterfaceAddress>lo</NetworkInterfaceAddress>  <!-- This line -->
            <AllowMulticast>default</AllowMulticast>
            <MaxMessageSize>65500B</MaxMessageSize>
            <FragmentSize>4000B</FragmentSize>
        </General>
        <Internal>
            <Watermarks>
                <WhcHigh>500kB</WhcHigh>
            </Watermarks>
            <AssumeMulticastCapable>lo</AssumeMulticastCapable>    <!-- And this line -->
        </Internal>
    </Domain>
</CycloneDDS>
``

#Source ros2 and 

``export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp``

``export CYCLONEDDS_URI=file://path/ddscyclonedds.xml``


# Explicitly activate multicast on loopback on Unix systems doing:

``sudo route add -net 224.0.0.0 netmask 240.0.0.0 dev lo``

``sudo ifconfig lo multicast``

# On k3lso quadruped run

``ros2 run zenoh_bridge_dds zenoh_bridge_dds --no-multicast-scouting -l udp/0.0.0.0:7447 -e udp/192.168.86.37:7447``

# In the base station

``ros2 run zenoh_bridge_dds zenoh_bridge_dds --no-multicast-scouting -l udp/0.0.0.0:7447``


# Pace the traffic

The traffic between ROS2 nodes can be intense and not compatible with the bandwidth or the message rate limitations imposed by some transports.

First, not all ROS2/DDS topics need be routed by the zenoh/DDS bridge. You can control the list of topics that are allowed to be routed over zenoh using the bridge’s --allow option. This option takes a regular expression that should match the topic names to be routed.
Here is an example of usage to allow a list of (fictitious) topics:

``--allow “position|speed|levels|race_flags|emergency_stop”``

Still, you might want to keep high frequency for intra-host publications, but downsampling those publications when routing them over a wireless transport.

For this purpose, we recently introduced a new option in the zenoh/DDS bridge:

--max-frequency <String>... : specifies a maximum frequency of data routing over zenoh per-topic.
The string must have the format “regex=float” where:
“regex” is a regular expression matching the set of ‘topic-name’ for which the data must be routed at no higher rate than associated max frequency.
“float” is the maximum frequency in Hertz; if publication rate is higher, downsampling will occur when routing.


#On k3lso quadruped run
  
#Now the full zenoh/DDS bridge commands will be:
  ``ros2 run zenoh_bridge_dds zenoh_bridge_dds --no-multicast-scouting -l udp/0.0.0.0:7447 \
  --allow "position|speed|levels|race_flags|emergency_stop" \
  --max-frequency "position|speed=10" \
  --max-frequency "levels=1"``
  
 #On Host 
  
  ``ros2 run zenoh_bridge_dds zenoh_bridge_dds --no-multicast-scouting -l udp/0.0.0.0:7447 \
  -e udp/192.168.86.37:7447 \
  --allow "position|speed|levels|race_flags|emergency_stop" \
  --max-frequency "race_flags=5" \
  --max-frequency "emergency_stop=20"``
  


































#Other notes
#Open port ubu

``sudo ufw enable``

``sudo ufw allow 7447``

check status ``sudo ufw status verbose``

