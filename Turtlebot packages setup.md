# Installing the Turtlebot packages
Following https://turtlebot.github.io/turtlebot4-user-manual

Execute in the docker container:
```
$ source /opt/ros/galactic/setup.bash
$ apt update
$ apt install ros-galactic-turtlebot4-desktop ros-galactic-turtlebot4-simulator
```

## Launch RVIZ
```
$ ros2 launch turtlebot4_viz view_model.launch.py
```

## Launch Gazebo
```
$ ros2 launch turtlebot4_ignition_bringup ignition.launch.py
```
