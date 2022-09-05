# ROS2 installation on Debian 12 (Bookworm)
**Note:** Debian 11 (Bullseye) is Tier 3 support, so the ROS team does not perform unit tests on it. Debian 12 (Bookworm) is unsupported and untested.

There are no pre-built binaries of ROS2 on Debian, so building from source is required. The procedure was adapted from https://docs.ros.org/en/galactic/Installation/Alternatives/Ubuntu-Development-Setup.html with slight Debian-specific modifications.

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

