 # ROS2 installation on Debian 12 (Bookworm) using Docker
This procedure was adapted from https://docs.ros.org/en/galactic/Installation/Ubuntu-Install-Debians.html with modifications to work on Docker.

## Constructing the Dockerfile
Create a Dockerfile with the following contents:

```
FROM ubuntu:focal

ARG DEBIAN_FRONTEND=noninteractive
```

### Set Locale
```
RUN apt-get update && apt-get install locales -y
RUN locale-gen en_US en_US.UTF-8
RUN update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
ENV LANG en_US.UTF-8
```

### Add the ROS package repository to `sources.list`
```
RUN apt-get install curl gnupg lsb-release -y
RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | tee /etc/apt/sources.list.d/ros2.list > /dev/null
```
### Installing ROS2 from the repository
We have to set up `tzdata` first, since it has weird interactive shell stuff going on.
```
RUN ln -snf /usr/share/zoneinfo/America/New_York /etc/localtime && echo America/New_York > /etc/timezone
RUN apt-get -y install tzdata
```
Then install ROS2
```
RUN apt-get update && apt-get upgrade -y
RUN apt-get install ros-galactic-desktop -y
```

## Building the Docker image
You can change the tag `user/ros-galactic` to whatever you want. Its purpose is to give the image an easy to remember name.
```
$ docker build -t user/ros-galactic .
```

## Running commands in the container
You can run commands in a Docker container with `docker run` but in order to get graphical applications to work correctly, we need to forward X server information.

First enable other local users to access the current X server:
```
$ xhost +local:*
```

To remove access, run:
```
$ xhost -local:*
```

The command below will start a GUI-capable shell in the container:
```
$ docker run --rm -e DISPLAY=$DISPLAY -v /tmp/.X11-unix/:/tmp/.X11-unix/ -it user/ros-galactic
```

* `--rm` tells docker to delete the container after it exits (for testing purposes)
* `-e DISPLAY=$DISPLAY` will give the container the `$DISPLAY` environment variable for your existing X server
* `-v /tmp/.X11-unix/:/tmp/.X11-unix/` will forward the X11 socket as a mounted volume in the docker container.
* `-i` makes an interactive session
* `-t` makes a pseudo-TTY (terminal)
* `user/ros-galactic` makes a container based on the `user/ros-galactic` image

## Try some examples
Open two shells in the docker image. In the first one, run:
```
$ source /opt/ros/galactic/setup.bash
$ ros2 run demo_nodes_cpp talker
```

In the second one, run:
```
$ source /opt/ros/galactic/setup.bash
$ ros2 run demo_nodes_py listener
```
The `talker` should be publishing messages and the `listener` should be receiving the messages and printing it to the screen.
