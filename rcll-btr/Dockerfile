FROM --platform=linux/x86_64 tiryoh/ros-desktop-vnc:noetic
LABEL maintainer="Wataru Uemmura<wataru@kdel.org>"

RUN apt-get update -q && \
    apt-get upgrade -yq && \
    DEBIAN_FRONTEND=noninteractive apt-get install -yq \
                     net-tools openssh-server git gcc build-essential vim screen ccache \
                     libmodbus-dev \
                     protobuf-compiler libprotobuf-dev libprotoc-dev \
                     libboost-all-dev libmodbus-dev \
                     libglibmm-2.4-dev libgtkmm-3.0-dev \
                     libncursesw5-dev libyaml-cpp-dev libavahi-client-dev \
                     libssl-dev libgecode-dev \
                     libncurses5-dev cmake-curses-gui \
                     libmbedtls-dev g++ libtinfo5 \
                     libspdlog-dev libspdlog1&& \
    rm -rf /var/lib/apt/lists/*
#
### install CLIPS
RUN mkdir -p /tmp/clips && cd /tmp/clips && \
    wget http://ppa.launchpad.net/timn/clips/ubuntu/pool/main/c/clips/clips_6.30-2ppa4~bionic1_amd64.deb && \
    wget http://ppa.launchpad.net/timn/clips/ubuntu/pool/main/c/clips/clips-common_6.30-2ppa4~bionic1_all.deb && \
    wget http://ppa.launchpad.net/timn/clips/ubuntu/pool/main/c/clips/libclips_6.30-2ppa4~bionic1_amd64.deb && \
    wget http://ppa.launchpad.net/timn/clips/ubuntu/pool/main/c/clips/libclips-dev_6.30-2ppa4~bionic1_amd64.deb && \
    wget http://ppa.launchpad.net/timn/clips/ubuntu/pool/main/c/clips/xclips_6.30-2ppa4~bionic1_amd64.deb && \
    wget http://ppa.launchpad.net/timn/clips/ubuntu/pool/main/libc/libclipsmm/libclipsmm-dev_0.3.4-1ppa1~bionic1_amd64.deb && \
    wget http://ppa.launchpad.net/timn/clips/ubuntu/pool/main/libc/libclipsmm/libclipsmm_0.3.4-1ppa1~bionic1_amd64.deb && \
    dpkg -i *.deb && \
    rm -rf /tmp/clips
#
### install opcua
RUN mkdir -p /tmp/opcua && cd /tmp/opcua && \
    git clone https://github.com/freeopcua/freeopcua && \
    cd /tmp/opcua/freeopcua && \
    cmake . && \
    make && sudo make install && sudo ldconfig && \
    rm -rf /tmp/opcua
#
### add user 
RUN useradd --create-home --home-dir /home/robotino --shell /bin/bash --user-group --groups adm,sudo robotino && \
    echo robotino:robotino | chpasswd && \
    echo "robotino ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
# COPY ./ros-noetic-desktop.sh /ros-noetic-desktop.sh
# RUN mkdir -p /tmp/ros_setup_scripts_ubuntu && mv /ros-noetic-desktop.sh /tmp/ros_setup_scripts_ubuntu/ && \
#     gosu robotino /tmp/ros_setup_scripts_ubuntu/ros-noetic-desktop.sh && \
#     rm -rf /var/lib/apt/lists/*
# 
### install refbox
RUN cd /home/robotino && \
    apt-get update -q && \
    DEBIAN_FRONTEND=noninteractive apt-get install -yq \
      libmicrohttpd-dev rapidjson-dev libaprutil1-dev libwebsocketpp-dev && \
    git clone https://github.com/robocup-logistics/rcll-refbox.git && \
    git clone https://github.com/wadaru/docker-config && \
    cd /home/robotino/rcll-refbox && \
    git checkout d0bcd590ab140691645fe8b651a3d00afad39151 && \
    cp /home/robotino/docker-config/rcll-btr/rcll-refbox/src/libs/mps_comm/opcua/opc_utils.cpp /home/robotino/rcll-refbox/src/libs/mps_comm/opcua/ && \
    cp /home/robotino/docker-config/rcll-btr/rcll-refbox/etc/buildsys/config.mk /home/robotino/rcll-refbox/etc/buildsys/ && \
    make  && \
    rm -rf /home/robotino/docker-config
#
### install RobView
RUN wget -qO - http://packages.openrobotino.org/keyFile | sudo apt-key add - && \
    sh -c 'echo "deb http://packages2.openrobotino.org $(lsb_release -sc) main" > /etc/apt/sources.list.d/openrobotino.list' && \
    apt-get update -q && apt-get install -yq robview4 aruco libopen62541 librealsense2 urg-library
#
### install ros for BabyTigers-R
RUN apt-get update -q && \
    DEBIAN_FRONTEND=noninteractive apt-get install -yq \
      ros-noetic-actionlib ros-noetic-pcl-ros ros-noetic-geometry ros-noetic-image-common \
      ros-noetic-image-transport-plugins ros-noetic-navigation \
      libpcl-conversions-dev libtf-conversions-dev libvisualization-msgs-dev \
      libudev-dev curl libtolua-dev
    # rosdep init && \
    # rosdep update
#
### for rplidar
RUN apt-get update -q && \
    DEBIAN_FRONTEND=noninteractive apt-get install -yq \
      ros-noetic-catkin ros-noetic-rplidar-ros && \
    mkdir -p /home/robotino/catkin_ws/src && \
    cd /home/robotino/catkin_ws/src && \
    /bin/bash -c "source /opt/ros/noetic/setup.bash && /opt/ros/noetic/bin/catkin_init_workspace" && \
    cd /home/robotino/catkin_ws && \
    /bin/bash -c "source /opt/ros/noetic/setup.bash && /opt/ros/noetic/bin/catkin_make" && \
    cd /home/robotino/catkin_ws/src && \
    git clone https://github.com/Slamtec/rplidar_ros.git && \
    cd /home/robotino/catkin_ws && \
    /bin/bash -c "source /opt/ros/noetic/setup.bash && /opt/ros/noetic/bin/catkin_make" && \
    echo "source ~/catkin_ws/devel/setup.bash" >> /home/robotino/.bashrc  && \
    chown -R robotino:robotino /home/robotino

#
### 
ENV USER robotino
