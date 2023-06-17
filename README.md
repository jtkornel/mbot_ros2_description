mbot_description
==========================================

Robot description for Makeblock mbot Ranger robots

![Licence](https://img.shields.io/badge/License-Apache-2.0-blue.svg)

# Quick Start Instructions

If you are familiar with ROS2, here are the quick-and-dirty build instructions.

  ```
  cd $COLCON_WS
  git clone git@github.com:mbot_description/mbot_description.git src/mbot_description
  vcs import src --input src/mbot_description/mbot_description.<ros-distro>.repos
  rosdep install --ignore-src --from-paths src -y -r
  colcon build --cmake-args -DCMAKE_BUILD_TYPE=Release    # Faster and more efficient build type
  cd ..
  ```
If you end up with missing dependencies, install them using commands from [Setup ROS Workspace](#setup-ros-workspace) section.

# How to use this Package and ROS Introduction

 - [Install and Build](#install-and-build)
   * [Install ROS Humble and Development Tooling](#install-ros-$ros_distro$-and-development-tooling)
   * [Setup ROS Workspace](#setup-ros-workspace)
   * [Configure and Build Workspace](#configure-and-build-workspace)
 - [Running Executables](#running-executables)
   * [Using the Local Workspace](#using-the-local-workspace)
 - [Testing and Linting](#testing-and-linting)
 - [Creating a new ROS2 Package](#creating-a-new-ros2-package)
 - [References](#references)

## Install and Build

### Install ROS Humble and Development Tooling

These instructions assume you are running Ubuntu 20.04:

1. [Install ROS2 Humble](https://index.ros.org/doc/ros2/Installation/Humble/Linux-Install-Debians/).
   You can stop following along with the tutorial after you complete the section titled: [Environment setup](https://index.ros.org/doc/ros2/Installation/Humble/Linux-Install-Debians/#environment-setup).
   Make sure you setup your environment with:
   ```
   source /opt/ros/$ros_distro$/setup.bash
   ```

   > **NOTE:** You may want to add that line to your `~/.bashrc`

   > **NOTE:** There is also a `zsh` version of the setup script.

1. [Install ROS2 Build Tools](https://index.ros.org/doc/ros2/Installation/Humble/Linux-Development-Setup/#install-development-tools-and-ros-tools).
   You do not need to build ROS2 from source.
   Simply install the tooling under the section titled "Install development tools and ROS tools".

1. Install `ccache`:
   ```
   sudo apt install ccache
   ```

1. Setup `colcon mixin` [Reference](https://github.com/colcon/colcon-mixin-repository) for convenience commands.
   ```
   sudo apt install python3-colcon-mixin
   colcon mixin add default https://raw.githubusercontent.com/colcon/colcon-mixin-repository/master/index.yaml
   colcon mixin update default
   ```

### Setup ROS Workspace

1. Create a colcon workspace:
   ```
   export COLCON_WS=~/workspace/ros_ws_foxy
   mkdir -p $COLCON_WS/src
   ```

   > **NOTE:** Feel free to change `~/workspace/ros_ws_foxy` to whatever absolute path you want.

   > **NOTE:** Over time you will probably have multiple ROS workspaces, so it makes sense to them all in a subfolder.
     Also, it is good practice to put the ROS version in the name of the workspace, for different tests you could just add a suffix to the base name `ros_ws_foxy`.

1. Download the required repositories and install package dependencies:
   ```
   cd $COLCON_WS
   git clone git@github.com:mbot_description/mbot_description.git src/mbot_description
   vcs import src --input src/mbot_description/mbot_description.<ros_distro>.repos
   rosdep install --ignore-src --from-paths src -y -r       # install also is there are unreleased packages
   ```

   Sometimes packages do not list all their dependencies so `rosdep` will not install everything.
   If you are getting missing dependency errors, try manually install the following packages:
   ```
   sudo apt install ros2-foxy-forward_command_controller ros2-foxy-joint_state_broadcaster ros2-foxy-joint_trajectory_controller ros2-foxy-xacro
   ```

### Configure and Build Workspace:
To configure and build workspace execute following commands:
  ```
  cd $COLCON_WS
  colcon build --symlink-install --mixin rel-with-deb-info compile-commands ccache
  ```

## Running Executable

See `README.md` files of the packages for information regarding running executables.

<Add here some concrete data about current repository>

### Using the Local Workspace

To use the local workspace you have to source it by using local setup script:
  ```
  source $COLCON_WS/install/local_setup.bash
  ```
Since there are many errors one unintentionally do with wrong sourcing, please check also [Notes on Sourcing ROS Workspace](#notes-on-sourcing-ros-workspace).

#### Notes on Sourcing ROS Workspace

Sourcing of a workspace appends the binary and resource directories to appropriate environment variables.
It is important that you do not run the build command in the same terminal that you have previously sourced your local workspace.
This can cause dependency resolution issues.
Here is some advice copied from [Official ROS Workspace Tutorial](https://index.ros.org/doc/ros2/Tutorials/Workspace/Creating-A-Workspace/) on this:

Before sourcing the overlay, it is very important that you open a new terminal, separate from the one where you built the workspace.
Sourcing an overlay in the same terminal where you built, or likewise building where an overlay is sourced, may create complex issues.

Sourcing the local_setup of the overlay will only add the packages available in the overlay to your environment.
`setup` sources the overlay as well as the underlay it was created in, allowing you to utilize both workspaces.

So, sourcing your main ROS 2 installation’s setup and then the dev_ws overlay’s local_setup, like you just did, is the same as just sourcing dev_ws’s setup, because that includes the environment of the underlay it was created in.


## Testing and Linting

To test the packages packages built from source, use the following command with [colcon](https://colcon.readthedocs.io/en/released/).
In order to run tests and linters you will have had to already built the workspace.
To run the tests use following commands:
  ```
  cd $COLCON_WS
  colcon test
  colcon test-result
  ```

There are `--mixin` arguments that can be used to control testing with linters, specifically `linters-only` and `linters-skip`.

## Creating a new ROS2 Package

If you need to create a new ros2 package it is helpful to start with the official boilerplate for a ros2 package.
The command `ros2 pkg` can be used to generate the boilerplate details.
For example to create a new ros2 package called `example_package` with a node called `example_node` and library called `example_library` use this command:
  ```
  ros2 pkg create --build-type ament_cmake --node-name example_node --library-name example_library example_package
  ```

## References

Here are some useful references for developing with ROS2:

 - [Official ROS2 Tutorials](https://index.ros.org/doc/ros2/Tutorials/)
   * [Luanchfile](https://index.ros.org/doc/ros2/Tutorials/Launch-Files/Creating-Launch-Files/)
   * [Package](https://index.ros.org/doc/ros2/Tutorials/Creating-Your-First-ROS2-Package/)
   * [Parameters](https://index.ros.org/doc/ros2/Tutorials/Parameters/Understanding-ROS2-Parameters/)
   * [Workspace](https://index.ros.org/doc/ros2/Tutorials/Workspace/Creating-A-Workspace/)
 - [Example ROS packages](https://github.com/ros2/examples)
 - [Colcon Documentation](https://colcon.readthedocs.io/en/released/#)
 - [ROS2 Design Documentation](https://design.ros2.org/)
 - [ROS2 Launch Architecture](https://github.com/ros2/launch/blob/master/launch/doc/source/architecture.rst)


## General details about robot description packages

This description package follows, as much as possible, the recommendations from [ROS-Industrial Consortium about robot support packages](https://wiki.ros.org/Industrial/Tutorials/WorkingWithRosIndustrialRobotSupportPackages).
The general package structure is the following:

```
<manufacturer|robot_name>_description/             # Robot's description files
├── [CMakeLists.txt]                               # if ament_cmake is used (recommended)
├── package.xml
├── [setup.py]                                     # if amend_python is used
├── [setup.cfg]                                    # if amend_python is used
├── config/                                        # general YAML files for a robot
│   └── <robot_name>_<someting_specific>.yaml
├── launch/                                        # launch files related to testing robots' description
│   └── test_<robot_name>_description.launch.py
├── meshes/                                        # meshes used in <robot_name>_macro.urdf.xacro
│   ├── collision
│   │   └── <robot_name|robot_model>               # meshes are sorted by robot name or model
│   │       ├── <link_xy>.stl
│   │       └── ...
│   └── visual
│       └── <robot_name|robot_model>
│           ├── <link_xy>.dae
│           └── ...
├── rviz/                                          # rviz display configurations
│   └── <robot_name>_default.rviz
└── urdf/                                          # URDF file for the robot
    ├── common.xacro                               # Common XACRO definitions
    ├── <robot_name>.urdf.xacro                    # Main URDF for a robot - loads macro and other files
    └── <robot_name|robot_model>
        ├── <robot_name>_macro.xacro               # Macro file of the robot - can add prefix, define origin, etc.
        └── <robot_name>_macro.ros2_control.xacro  # URDF-part used to configure ros2_control

```

### Testing the validity of robot description

1. Go to the root of your workspace folder (there where `src`, `build`, `install` and `log` files are).
2. Install the package by calling `colcon build --symlink-install --packages-select mbot_description`
3. (Re-)Source environment `source install/setup.bash`
4. Launch description test:
   ```
   ros2 launch mbot_description test_mbot_ranger_dashing_raptor_description.launch.py
   ```

If there are no issues with the description, two windows are opened: `rviz2` and `Joint State Publisher`.
Rviz2 visualizes the robot's state and Joint state Publisher to changes joint values using sliders or generates random but valid configurations.
