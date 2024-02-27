FROM tiryoh/ros-desktop-vnc:melodic
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
                     libspdlog-dev && \
    rm -rf /var/lib/apt/lists/*
RUN DEBIAN_FRONTEND=noninteractive apt-get install -yq \
                     libspdlog1 && \
    rm -rf /var/lib/apt/lists/*
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

RUN useradd --create-home --home-dir /home/robotino --shell /bin/bash --user-group --groups adm,sudo robotino && \
    echo robotino:robotino | chpasswd && \
    echo "robotino ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
# COPY ./ros-noetic-desktop.sh /ros-noetic-desktop.sh
# RUN mkdir -p /tmp/ros_setup_scripts_ubuntu && mv /ros-noetic-desktop.sh /tmp/ros_setup_scripts_ubuntu/ && \
    gosu robotino /tmp/ros_setup_scripts_ubuntu/ros-noetic-desktop.sh && \
    rm -rf /var/lib/apt/lists/*
ENV USER robotino
