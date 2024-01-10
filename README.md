# dv_ros_onboard

ros driver of dv xplorer event camera and instruction

## Source code
The source codes of libraries and ROS drivers can be obtained at Inivation's [official repository](https://gitlab.com/inivation/dv).

## Installation instruction

### DV-ROS installation

A detailed installation will be updated in Olaya's [blog](https://olayasturias.github.io/phdstuff/cpp/2024/01/02/the-cpp-diaries-2.html)

1. Install ROS and dependency like normally with apt
2. Install Cmake 3.22 or newer
3. Install gcc-10 and g++-10 (no need to make it default)
4. Create a separate directory, let's say "dv-dev" to store all the custom libraries and source code
5. Inside dv-dev, make libboost1.83 from source, without install into the system, as follow:
- download the correct libboost1.83 (or any version > 1.76)
- extract and go into the extracted directory
- run these lines:
```
./bootstrap.sh --prefix=/path/to/custom/boost
./b2 install
```
Replace /path/to/custom/boost with the directory where you want to install Boost. It should be created inside the "dv-dev" directory (e.g. dv-dev/boost1_83)
6. Download and install Eigen 3.4.0. Again, I prefer to keep it as a separate directory inside "dv-dev"
7. Now, clone the source code of the following libraries:
- [libcaer](https://gitlab.com/inivation/dv/libcaer)
- [dv-processing](git clone https://gitlab.com/inivation/dv/dv-processing)
- [dv-runtime](https://gitlab.com/inivation/dv/dv-runtime)

8. When building them with cmake, please use the following flag (when neeeded):
```
cmake -DCMAKE_C_COMPILER=gcc-10 -DCMAKE_CXX_COMPILER=g++-10 -DCMAKE_PREFIX_PATH="~/dv-dev/boost_1_84_dir;~/dv-dev/eigen-3_4_0;/usr"
```

and of course, "make install" them into the system

9. After successfully build the libraries (dv-proessing and dv-runtime), it is ready to build the ros package, with a catch: now you also have to specify the additional libraries for them. And it is funny that we have to use catkin_make to make it work

```
catkin_make -DCMAKE_C_COMPILER=gcc-10 -DCMAKE_CXX_COMPILER=g++-10 -DCMAKE_PREFIX_PATH="/home/thor/dv_event_cam_ws/boost_1_84_dir;/home/thor/dv_event_cam_ws/eigen-3_4_0;/opt/ros/noetic"
```

10. Now it should be plug and play all over again : )
```
source devel/setup.bash
roslaunch dv_ros_visualization event_visualization.launch
```

Bug: If you use catkin build insteads, whenever you source from the folder, it will overide the /opt/ros/noetic/setup.bash, and will no longer recognize "roslauch" or "roscore". It is likely related to extendability of the workspace. Will investigate this.