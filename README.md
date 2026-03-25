# ROS2 l ArduPilot l Gazebo l RViz l Drone Flight Testing
This guide aims to simulate a basic drone flight application using Gazebo and RViz.

## 1.
First, open a new terminal and enter the commands required to launch the Gazebo and RViz applications.

```sh

cd ~/ardu_ws
source install/setup.bash
ros2 launch ardupilot_gz_bringup iris_runway.launch.py

```

## 2.
Then, we will open a new terminal and place the necessary Python3 code into the test 1 folder.

```sh
nano trial1
```
- PASTE

```sh
from pymavlink import mavutil
import time

print("The drone is connecting...")

master = mavutil.mavlink_connection('udp:127.0.0.1:14551')
master.wait_heartbeat()

print("Connected")

# GUIDED mode
mode = 'GUIDED'
mode_id = master.mode_mapping()[mode]

master.mav.set_mode_send(
    master.target_system,
    mavutil.mavlink.MAV_MODE_FLAG_CUSTOM_MODE_ENABLED,
    mode_id
)

time.sleep(2)

# ARM
master.arducopter_arm()
master.motors_armed_wait()

print("Motors are on")

# TAKEOFF
master.mav.command_long_send(
    master.target_system,
    master.target_component,
    mavutil.mavlink.MAV_CMD_NAV_TAKEOFF,
    0,
    0,0,0,0,
    0,0,5
)

print("Taking off")

time.sleep(8)


def move(vx,vy,vz,duration):

    for i in range(duration*5):

        master.mav.set_position_target_local_ned_send(
            0,
            master.target_system,
            master.target_component,
            mavutil.mavlink.MAV_FRAME_LOCAL_NED,
            0b0000111111000111,
            0,0,0,
            vx,vy,vz,
            0,0,0,
            0,0
        )

        time.sleep(0.2)


print("Moving forward")
move(2,0,0,5)

print("Moving right")
move(0,2,0,5)

print("Moving backward")
move(-2,0,0,5)

print("Moving left")
move(0,-2,0,5)


print("Landing")

master.mav.command_long_send(
    master.target_system,
    master.target_component,
    mavutil.mavlink.MAV_CMD_NAV_LAND,
    0,
    0,0,0,0,
    0,0,0
)

```

- ctrl + O (SAVE)
- ENTER
- ctrl + X (EXIT)


## 3.
Now we can fly the drone.

```sh

python3 trial1

```
The drone has successfully executed the given commands and landed.
















