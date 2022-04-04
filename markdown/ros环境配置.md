# ROS相关安装及在NUC上的部署

## 1.Ubuntu20.04 ROS Noetic安装

参考官方安装教程

[ROS Installation](http://wiki.ros.org/cn/Installation )

请以以上安装步骤为准，因为随着版本更新有些步骤可能已改，我也是复制上面的步骤，不过把一些我们用不到的指令和解释去掉而已

##### 1.1 配置Ubuntu软件仓库

配置你的Ubuntu软件仓库（repositories）以允许使用“restricted”“universe”和“multiverse”存储库。然后把软件源切换到清华源。

##### 1.2 设置sources.list

设置电脑以安装来自packages.ros.org的软件。

```shell
sudo sh -c '. /etc/lsb-release && echo "deb http://mirrors.tuna.tsinghua.edu.cn/ros/ubuntu/ `lsb_release -cs` main" > /etc/apt/sources.list.d/ros-latest.list'
```

##### 1.3 设置密钥

```shell
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
```

##### 1.4 开始安装

首先，确保你的Debian软件包索引是最新的：

```shell
sudo apt update
```

然后安装桌面版安装

```shell
sudo apt install ros-noetic-desktop-full
```

##### 1.5 设置环境

你需要在使用ROS的每个**bash**终端中source这个脚本。

```shell
source /opt/ros/noetic/setup.bash
```

或者用下列指令让每次打开**bash**时自动source这个脚本:

```shell
echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

# 2.Realsense-ROS环境配置

参考博客

[Ubuntu20.04+RealSense D435i](https://blog.csdn.net/weixin_41720528/article/details/114268106)

realsense librealsense官方的安装教程我照着安装有问题，但是上面博客的步骤我试了是可以的。

##### 2.1安装RealSense SDK

首先安装以下几个包

```shell
sudo apt-get install libudev-dev pkg-config libgtk-3-dev
sudo apt-get install libusb-1.0-0-dev pkg-config
sudo apt-get install libglfw3-dev
sudo apt-get install libssl-dev
```

然后拉取librealsense

```shell
git clone https://github.com/IntelRealSense/librealsense.git
```

然后去拉取的目录安装依赖项和编译

```shell
cd librealsense
sudo cp config/99-realsense-libusb.rules /etc/udev/rules.d/
sudo udevadm control --reload-rules && udevadm trigger 
mkdir build
cd build
cmake ../ -DBUILD_EXAMPLES=true
make
sudo make install
```

然后连接摄像头，输入以下指令来查看一下是否安装成功

```shell
realsense-viewer 
```

##### 2.2 ROS包安装

进入工作空间，如果之前没有创建工作空间请先[创建一个工作空间](http://wiki.ros.org/cn/catkin/Tutorials/create_a_workspace)，我的工作空间路径是`~/catkin_ws`,所以按如下指令安装

```shell
cd ~/catkin_ws/src
git clone https://github.com/IntelRealSense/realsense-ros.git
git clone https://github.com/pal-robotics/ddynamic_reconfigure.git
cd ~/catkin_ws && catkin_make 
```

然后可以在ros中启动一个demo试试

首先运行roscore

```shell
roscore
```

然后运行点云demo

```shell
roslaunch realsense2_camera demo_pointcloud.launch 
```

运行这个时，可能会报错说没有这个包，这是因为我们没有把ros的工作空间切换到我们之前创建的工作空间，请到工作空间的目录下运行以下指令

```shell
cd ~/catkin_ws
source devel/setup.bash
```

运行如下指令来启动realsense相机节点:

```shell
roslaunch realsense2_camera rs_camera.launch
```

这将流式传输所有相机传感器并在适当的 ROS 主题上发布。

# 3.多主机ROS通信

ROS可以在一个主机上运行Master，然后其他从机连接到该主机的Master

在主机上在运行`roscore`时先设置如下环境变量

```shell
export ROS_HOSTNAME=[master_host_ip]
export ROS_MASTER_URI=http://[host_ip]:11311
```

其中`HOSTNMAME`是指主机名称(官方文档上是说随便起，但是我是直接把主机的ip当作名字了，别的我没试，不知道行不行)。

然后从机`roslaunch`之前也要设置如下的环境变量

```shell
export ROS_HOSTNAME=[slave_host_ip]
export ROS_MASTER_URI=http://[master_host_ip]:11311
```

在多机器人通信中，`ROS_MASTER_URI`是唯一的，都是指向主机的`ROS_MASTER_URI`的，但`ROS_HOSTNAME`是每台机器都不同的

这些设置好后可以`roslaunch` realsense的节点来启动realsense的服务和话题，以下指令是默认参数启动

```shell
roslaunch realsense2_camera rs_camera.launch
```

考虑到多个相机往Master发布话题的带宽问题，我们可以在启动时设置一些参数来降低话题带宽，例如我的启动设置

```shell
roslaunch realsense2_camera rs_camera.launch color_width:=640 color_height:=480 color_fps:=15 \
depth_width:=640 depth_height:=480 depth_fps:=15
```

这样深度图像和彩色图像的分辨率均设置为640*480，刷新率均设置位15fps。

多个主机同时启动rs_camera时，节点名称默认是一样的，这样会造成冲突，所以在启动之前要修改名称。

打开catkin工作目录下的 `src/realsense-ros/realsense2_camera/launch/rs_camera.launch`文件

将文件中的

```xml
<group ns="$(arg_camera)">
```

修改为你的自定义名字

```xml
<group ns="name">
```

