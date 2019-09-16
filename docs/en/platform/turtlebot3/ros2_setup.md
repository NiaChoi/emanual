---
layout: archive
lang: en
ref: ros_2
read_time: true
share: true
author_profile: false
permalink: /docs/en/platform/turtlebot3/ros2_setup/
sidebar:
  title: TurtleBot3
  nav: "turtlebot3"
product_group: turtlebot3
page_number: 26
---

<div style="counter-reset: h1 14"></div>

# [Setup](#setup)

{% capture notice_01 %}
**NOTE**:
- The test is done on `Ubuntu 18.04` and `ROS 2 Dashing Diademata`.
- These instructions are supposed to be performed with a remote PC and SBC in TurtleBot.
- You can ask any questions on [ROS Answers](https://answers.ros.org/questions/) if you are in touble to follow the instruction.
{% endcapture %}
<div class="notice--info">{{ notice_01 | markdownify }}</div>

**TIP**: The terminal application can be found with the Ubuntu search icon on the top left corner of the screen. The shortcut key for running the terminal is `Ctrl`-`Alt`-`T`.
{: .notice--success}

This chapter shows demos using TurtleBot3 with **ROS 2** and **Gazebo 9**. In order to implement them, you need to install some packages.

## [PC Setup](#pc-setup)

### Install Ubuntu on Remote PC

To set ROS (Robot Operating System) on **Remote PC**, please install Ubuntu 18.04 on **Remote PC**. Tutorial guide will be helpful for you to install it.

- [Download Ubuntu 18.04](http://releases.ubuntu.com/18.04/)
- [Tutorial - Install Ubuntu Desktop](https://tutorials.ubuntu.com/tutorial/tutorial-install-ubuntu-desktop#0)

### Install ROS 2 on Remote PC

![](/assets/images/platform/turtlebot3/logo_ros.png)

As TurtleBot3 operates on Robot Operating System(ROS), it requies to intall `ros-dashing-desktop` debian packages for `ROS 2 Dashing Diademata` on Ubuntu installed in **Remote PC**. Following link will guide you for installing ROS 2.

- [ROS 2 Installation Guide](https://index.ros.org/doc/ros2/Installation/Dashing/)

### Install ROS 2 Dependency Packages

Install ROS 2 dependency packages on **Remote PC**.

```bash
# Install Cartographer dependencies
$ sudo apt install -y \
    google-mock \
    libceres-dev \
    liblua5.3-dev \
    libboost-dev \
    libboost-iostreams-dev \
    libprotobuf-dev \
    protobuf-compiler \
    libcairo2-dev \
    libpcl-dev \
    python3-sphinx
# Install Gazebo9
$ curl -sSL http://get.gazebosim.org | sh
$ sudo apt install ros-dashing-gazebo-*
# Install Cartographer
$ sudo apt install ros-dashing-cartographer
# Install Navigation2
$ sudo apt install ros-dashing-navigation2
$ sudo apt install ros-dashing-nav2-bringup
# Install vcstool
$ sudo apt install python3-vcstool
```

### Install TurtleBot3 ROS 2 Packages

Download `turtlebot3` packages and install `turtlebot3` packages and ROS 2 dependency packages on **Remote PC**.

```bash
$ mkdir -p ~/turtlebot3_ws/src
$ cd ~/turtlebot3_ws
$ wget https://raw.githubusercontent.com/ROBOTIS-GIT/turtlebot3/ros2/turtlebot3.repos
$ vcs import src < turtlebot3.repos
$ colcon build --symlink-install
```

### Save Bash Command for Setup
```bash
$ echo 'source ~/turtlebot3_ws/install/setup.bash' >> ~/.bashrc
$ echo 'export ROS_DOMAIN_ID=30 #TURTLEBOT3' >> ~/.bashrc
$ source ~/.bashrc
```

{% capture notice_02 %}
**NOTE**:
Related documents will be helpful for you to fix the problem of any build errors or warnings from dependencies files.  
**ROS related documents** :  
- [Cartographer](https://google-cartographer.readthedocs.io/en/latest/)
- [Cartographer_ros](https://google-cartographer-ros.readthedocs.io/en/latest/)
- [Navigation2](https://github.com/ros-planning/navigation2/blob/master/README.md)
{% endcapture %}
<div class="notice--info">{{ notice_02 | markdownify }}</div>

## [SBC Setup](#sbc-setup)

### Download and Install Ubuntu Image File

**WARNING** : Do not proceed to this instruction on SBC in TurtleBot3. Please follow steps with **Remote PC**.
{: .notice--warning}

1. Go on [Ubuntu releases](http://cdimage.ubuntu.com/ubuntu/releases/bionic/release) to download an image file.
2. Download `Raspberry Pi 3 (64-bit ARM) preinstalled server image` on **Remote PC**
3. Burn the Ubuntu image file to a microSD card.

**TIP** : You can use `GNOME Disks` to burn `Ubuntu Server 18.04 image` to microSD.
{: .notice}

### Initialization Process for Raspberry Pi 3

**WARNING** : Do not proceed to this instruction on remote PC. Please follow steps with **SBC in TurtleBot3**.
{: .notice--warning}

To communicate between **Remote PC** and **TurtleBot3**, install `Ubuntu Server 18.04 image` file on Raspberry Pi 3.
1. Boot up Raspberry Pi 3 after insert a microSD card which has the image file into a microSD card slot on SBC in TurtleBot3.  
  (You can connect HDMI cable, keyboard and mouse into the TurtleBot3)
2. Log in with default username(`ubuntu`) and password(`ubuntu`).  
  (After log in, system will ask you whether you change the password or not)
3. You can configure WiFi network setting by utilizing a [netplan](https://netplan.io/). Refer to [Example for Netwrok Configuration](#example-for-network-configuration).

**NOTE** : TurtleBot3 is a mobile robot to run [SLAM](/docs/en/platform/turtlebot3/slam/#slam) and [Navigation](/docs/en/platform/turtlebot3/navigation/#navigation) using wireless network, so that connecting TurtleBot3 to WIFI is recommanded.
{: .notice}

### Example for Network Configuration

1. Create a folder, and then open it with the following commands.
  ```bash
  $ sudo touch /etc/netplan/01-netcfg.yaml
  $ sudo nano /etc/netplan/01-netcfg.yaml
  ```

2. After opening the file, configure a network setting. Refer to the network setting below.
```yaml
network:
    version: 2
    renderer: networkd
    ethernets:
      eth0:
        dhcp4: yes
        dhcp6: yes
        optional: true
    wifis:
      wlan0:
        dhcp4: yes
        dhcp6: yes
        access-points:
          "your-wifi-name":
            password: "your-wifi-password"
```

3. After configuration, remote PC can connect to a SBC in TurtleBot3 by following steps.

4. Apply all configuration for the renderers, and then restart Raspberry Pi 3.
```bash
$ sudo netplan apply
```

5. Set the `systemd` to prevent boot-up delay even if there is no network at startup. Run a command below to set mask the `systemd` process using the following command.
```bash
$ systemctl mask systemd-networkd-wait-online.service
```
    **NOTE** : From now, you can use SSH. If you want remote PC to connect to SBC and to install ROS and TurtleBot3 software, run a command below.
    {: .notice}
```bash
$ ssh ubuntu@<NETWORK IP of Raspberry PI>
```

### Add Swap Space

1. Create a file while it will be used for swap
```bash
$ sudo fallocate -l 1G /swapfile
```

2. Give `read` and `write` permissions to the file
```bash
$ sudo chmod 600 /swapfile
```

3. Use `mkswap` to set up a Linux swap area. 
```bash
$ sudo mkswap /swapfile
```

4. After creating a file, activate the file to start using it. 
```bash
$ sudo swapon /swapfile
```

5. Open `/etc/fstab` file with `nano` command, as swap areas are listed in `/etc/fastab`.
```bash
$ sudo nano /etc/fstab
```

6. Copy a sentence below, then paste it the file you opened.
```
/swapfile swap swap defaults 0 0
```

7. Verify swap area with `free` syntax whether it is allocated. It will present a table consisting of six colums and three rows of data. 
```bash
$ sudo free -h
```

### Intall ROS 2 Dashing Diademata

**WARNING** : Do not proceed to this instruction on remote PC. Please follow steps with **SBC in TurtleBot3**.
{: .notice--warning}

As TurtleBot3 operates on Robot Operating System(ROS), it requies to intall `ROS 2 Dashing Diademata` on Ubuntu installed in **SBC of TurtleBot3**. Following link will guide you for installing ROS 2.

- [ROS 2 Installation Guide](https://index.ros.org/doc/ros2/Installation/Dashing/Linux-Install-Debians/)

1. Update and upgrade your software
```bash
$ sudo apt update && sudo apt upgrade
```
1. Setup locale
```bash
$ sudo locale-gen en_US en_US.UTF-8
$ sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
$ export LANG=en_US.UTF-8
```
1. Setup sources
```bash
$ sudo apt update && sudo apt install curl gnupg2 lsb-release
$ curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
$ sudo sh -c 'echo "deb [arch=amd64,arm64] http://packages.ros.org/ros2/ubuntu `lsb_release -cs` main" > /etc/apt/sources.list.d/ros2-latest.list'
```
1. Install ROS 2 packages
```bash
$ sudo apt update
$ sudo apt install ros-dashing-ros-base
```

### Install TurtleBot3 Packages

**WARNING** : Do not proceed to this instruction on remote PC. Please follow steps with **SBC in TurtleBot3**.
{: .notice--warning}

```bash
$ sudo apt install python3-argcomplete python3-colcon-common-extensions libboost-system-dev
$ mkdir -p ~/turtlebot3_ws/src && cd ~/turtlebot3_ws/src
$ git clone -b ros2 https://github.com/ROBOTIS-GIT/hls_lfcd_lds_driver.git
$ git clone -b ros2 https://github.com/ROBOTIS-GIT/turtlebot3_msgs.git
$ git clone -b ros2 https://github.com/ROBOTIS-GIT/turtlebot3.git
$ git clone -b ros2 https://github.com/ROBOTIS-GIT/DynamixelSDK.git
$ cd ~/turtlebot3_ws/src/turtlebot3
$ rm -r turtlebot3_cartographer turtlebot3_navigation2
$ cd ~/turtlebot3_ws/
$ source /opt/ros/dashing/setup.bash
$ colcon build --symlink-install --parallel-workers 1
```

### Environment Setup

#### Domain ID Allocation
In DDS communication, `ROS_DOMAIN_ID` must be matched between **Remote PC** and **TurtleBot3** for wireless communication under the same network environment. Following commands shows how to assign a `ROS_DOMAIN_ID` to SBC in TurtleBot3.
- A default ID of **TurtleBot3** is set as `0`.  
- To configure the `ROS_DOMAIN_ID` of Remote PC and SBC in TurtleBot3 to `30` is recommendable.  

```bash
$ echo 'source ~/turtlebot3_ws/install/setup.bash' >> ~/.bashrc
$ echo 'export ROS_DOMAIN_ID=30 #TURTLEBOT3' >> ~/.bashrc
$ source ~/.bashrc
```
**WARNING** : Do not **OVERLAP** any IDs between you and other users. It will cause a conflict of communication between users under the same network environment.
{: .notice--warning}

#### OpenCR Port Setup

Following commands show how to assign OpenCR port authorization to TurtleBot3.
```bash
$ cd ~/turtlebot3_ws/src/turtlebot3/turtlebot3_bringup 
$ sudo cp ./99-turtlebot3-cdc.rules /etc/udev/rules.d/ 
$ sudo udevadm control --reload-rules 
$ sudo udevadm trigger
```

### [References](#references)

[https://wiki.ubuntu.com/ARM/RaspberryPi](https://wiki.ubuntu.com/ARM/RaspberryPi)  
[https://discourse.ros.org/t/setting-up-the-turtlebot3-with-ros2-on-ubuntu-server-iot-18-04/10090](https://discourse.ros.org/t/setting-up-the-turtlebot3-with-ros2-on-ubuntu-server-iot-18-04/10090)  
[https://netplan.io](https://netplan.io)  
[https://index.ros.org/doc/ros2/Installation/Dashing/Linux-Install-Debians/](https://index.ros.org/doc/ros2/Installation/Dashing/Linux-Install-Debians/)  
[https://index.ros.org/doc/ros2/Tutorials/Colcon-Tutorial/](https://index.ros.org/doc/ros2/Tutorials/Colcon-Tutorial/)  
[https://linuxize.com/post/how-to-add-swap-space-on-ubuntu-18-04/](https://linuxize.com/post/how-to-add-swap-space-on-ubuntu-18-04/)

## [OpenCR Setup](#opencr-setup)

**WARNING** : Do not proceed to this instruction on remote PC. Please follow steps with **SBC in TurtleBot3**.
{: .notice--warning}

### Install Dependencies to Run 32bit Executables.
```bash
$ sudo dpkg --add-architecture armhf
$ sudo apt-get update
$ sudo apt-get install libc6:armhf
```

### Download OpenCR Binaries & Tools for Uploading.
```bash
$ cd && rm -rf opencr_update.tar.bz2
$ wget https://github.com/ROBOTIS-GIT/OpenCR-Binaries/raw/master/turtlebot3/ROS2/latest/opencr_update.tar.bz2
$ tar -xjf ./opencr_update.tar.bz2
```

### Upload ROS 2 Firmware of TurtleBot3 to OpenCR.

#### For TurtleBot3 Burger.
```bash
# Set a port for OpenCR 
$ export OPENCR_PORT=/dev/ttyACM0
# Set a model of TurtleBot3 you are using
$ export OPENCR_MODEL=burger
$ cd ~/opencr_update && ./update.sh $OPENCR_PORT $OPENCR_MODEL.opencr
```

The following window shows you the result of a firmware upload of TurtleBot3 Burger to OpenCR.  
Make sure `jump_to_fw` message is displayed on the bottom of the window. 

```bash
aarch64
arm
OpenCR Update Start..
opencr_ld_shell ver 1.0.0
opencr_ld_main
[  ] file name   	: burger.opencr
[  ] file size   	: 168 KB
[  ] fw_name     	: burger
[  ] fw_ver      	: V180903R1
[OK] Open port   	: /dev/ttyACM0
[  ]
[  ] Board Name  	: OpenCR R1.0
[  ] Board Ver   	: 0x17020800
[  ] Board Rev   	: 0x00000000
[OK] flash_erase 	: 0.96s
[OK] flash_write 	: 1.92s
[OK] CRC Check   	: 10E28C8 10E28C8 , 0.006000 sec
[OK] Download
[OK] jump_to_fw
```

#### For TurtleBot3 Waffle and Waffle Pi.

```bash
# Set a port for OpenCR 
$ export OPENCR_PORT=/dev/ttyACM0
# Set a model of TurtleBot3 you are using
$ export OPENCR_MODEL=waffle
$ cd ~/opencr_update && ./update.sh $OPENCR_PORT $OPENCR_MODEL.opencr
```

The following window shows you the result of a firmware upload of TurtleBot3 Waffle/Waffle Pi to OpenCR.  
Make sure `jump_to_fw` message is displayed on the bottom of the window.  

```bash
aarch64
arm
OpenCR Update Start..
opencr_ld_shell ver 1.0.0
opencr_ld_main
[  ] file name   	: waffle.opencr
[  ] file size   	: 168 KB
[  ] fw_name     	: waffle
[  ] fw_ver      	: V180903R1
[OK] Open port   	: /dev/ttyACM0
[  ]
[  ] Board Name  	: OpenCR R1.0
[  ] Board Ver   	: 0x17020800
[  ] Board Rev   	: 0x00000000
[OK] flash_erase 	: 0.96s
[OK] flash_write 	: 1.92s
[OK] CRC Check   	: 10E28C8 10E28C8 , 0.006000 sec
[OK] Download
[OK] jump_to_fw
```

Reset OpenCR using RESET button.

![](/assets/images/parts/controller/opencr10/bootloader_19.png)

After few seconds, particular sound will be played. 