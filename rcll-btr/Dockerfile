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
    DEBIAN_FRONTEND=noninteractive apt-get install -yq \
                     libmicrohttpd-dev rapidjson-dev libaprutil1-dev libwebsocketpp-dev gosu && \
    DEBIAN_FRONTEND=noninteractive apt-get install -yq \
      ros-noetic-actionlib ros-noetic-pcl-ros ros-noetic-geometry ros-noetic-image-common \
      ros-noetic-image-transport-plugins ros-noetic-navigation \
      libpcl-conversions-dev libtf-conversions-dev libvisualization-msgs-dev \
      libudev-dev curl libtolua-dev && \
    DEBIAN_FRONTEND=noninteractive apt-get install -yq \
      ros-noetic-catkin &&\
    DEBIAN_FRONTEND=noninteractive apt-get install -yq \
      gnome && \
    dpkg --add-architecture i386 && \
    apt-get update -q && \
    DEBIAN_FRONTEND=noninteractive apt-get install -yq \
      wine32 gosu
#    rm -rf /var/lib/apt/lists/*
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
    make -j"$(nproc)"&& sudo make install && sudo ldconfig && \
    rm -rf /tmp/opcua
#
### add user 
RUN useradd --create-home --home-dir /home/robotino --shell /bin/bash --user-group --groups adm,sudo robotino && \
    echo robotino:robotino | chpasswd && \
    echo "robotino ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
# 
### install refbox
RUN cd /home/robotino && \
    git clone https://github.com/robocup-logistics/rcll-refbox.git && \
    git clone https://github.com/wadaru/docker-config && \
    cd /home/robotino/rcll-refbox && \
    git checkout d0bcd590ab140691645fe8b651a3d00afad39151 && \
    cp /home/robotino/docker-config/rcll-btr/rcll-refbox/src/libs/mps_comm/opcua/opc_utils.cpp /home/robotino/rcll-refbox/src/libs/mps_comm/opcua/ && \
    cp /home/robotino/docker-config/rcll-btr/rcll-refbox/etc/buildsys/config.mk /home/robotino/rcll-refbox/etc/buildsys/ && \
    make -j"$(nproc)" && \
    rm -rf /home/robotino/docker-config && \
    chown -R robotino:robotino /home/robotino
#
### install RobView
RUN wget -qO - http://packages.openrobotino.org/keyFile | sudo apt-key add - && \
    sh -c 'echo "deb http://packages2.openrobotino.org $(lsb_release -sc) main" > /etc/apt/sources.list.d/openrobotino.list' && \
    apt-get update -q && \
    DEBIAN_FRONTEND=noninteractive apt-get install -yq \
        robview4 aruco libopen62541 librealsense2 urg-library
#
#
### for rplidar
RUN mkdir -p /home/robotino/catkin_ws/src && \
    chown -R robotino:robotino /home/robotino &&\
    cd /home/robotino/catkin_ws/src && \
    gosu robotino /bin/bash -c "source /opt/ros/noetic/setup.bash && /opt/ros/noetic/bin/catkin_init_workspace" && \
    cd /home/robotino/catkin_ws && \
    gosu robotino /bin/bash -c "source /opt/ros/noetic/setup.bash && /opt/ros/noetic/bin/catkin_make" && \
    cd /home/robotino/catkin_ws/src && \
    gosu robotino /bin/bash -c "git clone https://github.com/Slamtec/rplidar_ros.git" && \
    cd /home/robotino/catkin_ws && \
    gosu robotino /bin/bash -c "source /opt/ros/noetic/setup.bash && /opt/ros/noetic/bin/catkin_make" && \
    gosu robotino /bin/bash -c "echo 'source ~/catkin_ws/devel/setup.bash' >> /home/robotino/.bashrc"
    # chown -R robotino:robotino /home/robotino

#
### for team program of BabyTigers-R
RUN mkdir /home/robotino/git && \
    cd /home/robotino/git && \
    git clone https://github.com/wadaru/ros-rcll_refbox_peer && \
    git clone https://github.com/wadaru/ros-rcll_btr_msgs && \
    git clone https://github.com/wadaru/ros-rcll_ros_msgs && \
    git clone https://github.com/wadaru/btr2023 && \
    git clone https://github.com/babytigers-r/rcll && \
    git clone https://github.com/wadaru/docker-config && \
    ln -s /home/robotino/rcll-refbox/src/msgs /home/robotino/rcll-refbox/src/libs/llsf_msgs && \
    cd /home/robotino/catkin_ws/src && \
    ln -s /home/robotino/git/ros-rcll_refbox_peer . && \
    ln -s /home/robotino/git/ros-rcll_btr_msgs . && \
    ln -s /home/robotino/git/ros-rcll_ros_msgs . && \
    cp /home/robotino/git/docker-config/rcll-btr/ros-rcll_refbox_peer/CMakeLists.txt /home/robotino/git/ros-rcll_refbox_peer/ && \
    cd /home/robotino/catkin_ws && \
    chown robotino:robotino -R /home/robotino && \
    gosu robotino /bin/bash -c 'cd /home/robotino/rcll-refbox && make -j"$(nproc)" && cd /home/robotino/catkin_ws && source /home/robotino/.bashrc && source /home/robotino/catkin_ws/devel/setup.bash && /opt/ros/noetic/bin/catkin_make' 
#
#
### for robotino sim demo
RUN gosu robotino /bin/bash -c "echo '#!/bin/bash' > ~/robotinoSimDemo.sh" && \
    gosu robotino /bin/bash -c "echo 'if [ -d ~/.wine/drive_c/Program\ Files\ \(x86\)/Didactic/RobotinoSim\ Demo ] ; then wine ~/.wine/drive_c/Program\ Files\ \(x86\)/Didactic/RobotinoSim\ Demo/bin/robotinosim.exe; else wget https://doc.openrobotino.org/download/RobotinoSim/RobotinoSimDemo-1.4.2.exe && wine RobotinoSimDemo-1.4.2.exe; fi' >> ~/robotinoSimDemo.sh" && \
    chmod +x /home/robotino/robotinoSimDemo.sh 
#
#    
### for ssh server
RUN mkdir /var/run/sshd && \
    sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/g' /etc/ssh/sshd_config && \
    sed -i 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' /etc/pam.d/sshd && \
    update-rc.d ssh defaults &&\ 
    service ssh start && \
    echo "robotino:robotino" | chpasswd
#
#
#
RUN chown -R robotino:robotino /home/robotino
ENV USER robotino
EXPOSE 22
ENTRYPOINT ["/usr/sbin/sshd", "-D"]
