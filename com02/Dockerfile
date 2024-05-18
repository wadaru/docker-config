FROM tiryoh/ros-desktop-vnc:noetic
LABEL maintainer="Wataru Uemmura<wataru@kdel.org>"

RUN apt-get update -q && \
    apt-get upgrade -yq && \
    DEBIAN_FRONTEND=noninteractive apt-get install -yq openssh-server
RUN DEBIAN_FRONTEND=noninteractive apt-get install -yq curl xdg-utils && \
    curl -O https://packages.microsoft.com/repos/code/pool/main/c/code/code_1.89.1-1715058876_arm64.deb && \
    dpkg -i code_1.89.1-1715058876_arm64.deb

ENV USER robotino
ENV PASSWORD robotino
