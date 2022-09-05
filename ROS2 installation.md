# ROS2 installation on Debian 12 (Bookworm)
**Note:** The install procedure for installing ROS2 with conda may change soon because of https://github.com/ros-infrastructure/rosdep/pull/810

**Note:** Debian 11 (Bullseye) is Tier 3 support, so the ROS team does not perform unit tests on it and there are no prebuilt binaries. Debian 12 (Bookworm) is unsupported and untested.

**Warning:** I'm installing some packages from the bullseye repository, which is highly discouraged (see [*Don't Break Debian*](https://wiki.debian.org/DontBreakDebian#Don.27t_make_a_FrankenDebian)). It should be fine in this case since all the bullseye packages should stay inside the virtual environment.

This procedure was adapted from https://docs.ros.org/en/galactic/Installation/Alternatives/Ubuntu-Development-Setup.html with slight Debian-specific modifications.

## Setting up APT
TODO

## Virtual environments
I used Anaconda to organize the packages into virtual environments. This helps prevent conflicting packages with other projects.

### Creating a conda environment
Create a new environment called "eece5800" based on the `base` environment.

`$ conda create -n eece5800 --clone base`

Then switch to it using:

`$ conda activate eece5800`

Normally you want to install `pip` at this point, but I already have it in `base`. Just verify I'm using the right kind of pip:

```
(eece5800)$ which pip
/home/username/anaconda3/envs/eece5800/bin/pip
```

### Installing development tools and ROS tools
Instead of `apt install`, I used `conda install` to make sure the build dependencies are installed only into this environment. Some packages are not available from the main Anaconda repository so conda-forge is needed.

`(eece5800)$ conda config --add channels conda-forge`

followed by:

`(eece5800)$ conda install colcon-common-extensions flake8 pytest-cov rosdep vcstool setuptools`

`(eece5800)$  pip install -U flake8-blind-except flake8-builtins flake8-class-newline flake8-comprehensions flake8-deprecated flake8-docstrings flake8-import-order flake8-quotes pytest-repeat pytest-rerunfailures pytest setuptools
`
### Get the ROS2 code
```
(eece5800)$ mkdir -p ~/ros2_galactic/src
(eece5800)$ cd ~/ros2_galactic
(eece5800)$ vcs import --input https://raw.githubusercontent.com/ros2/ros2/galactic/ros2.repos src
```

### Setting up rosdep
`rosdep` doesn't work perfectly with conda so `rosdep init` has to be done manually ouside the container:

```
# mkdir -p /etc/ros/rosdep/sources.list.d/
# cd /etc/ros/rosdep/sources.list.d/
# wget https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/sources.list.d/20-default.list
```

Back to the container to finish setting up `rosdep`:

```
(eece5800)$ rosdep update
(eece5800)$ rosdep install --os=debian:bullseye --rosdistro galactic --from-paths src --ignore-src -y --skip-keys "fastcdr rti-connext-dds-5.3.1 urdfdom_headers"
```
`rosdep` will complain that there is no definition of `python3-ifcfg` for OS version `bullseye`. Debian has never included a package for `python3-ifcfg` so there's not much we can do regarding this.

This is a minor issue since the package is only used by `ros2doctor` to check the network configuration. Debian has the `ip` package to manually check network information.
