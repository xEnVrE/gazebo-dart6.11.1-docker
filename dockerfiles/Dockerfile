FROM ubuntu:focal
LABEL org.opencontainers.image.title="Gazebo 11 with DART 6.11.1"
LABEL org.opencontainers.image.description="Infrastructure for running Gazebo 11 with DART with support for friction coefficients"
LABEL org.opencontainers.image.authors="Nicola A. Piga <nicola.piga@iit.it>"

# Use /bin/bash instead of /bin/sh
SHELL ["/bin/bash", "-c"]

# Non-interactive installation mode
ENV DEBIAN_FRONTEND=noninteractive

# Update apt database
RUN apt update

# Set the locale
RUN apt install -y -qq apt-utils locales
RUN locale-gen en_US en_US.UTF-8
RUN update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
ENV LANG=en_US.UTF-8

# Install essentials
RUN apt install --no-install-recommends -y -qq build-essential ca-certificates cmake cmake-curses-gui curl emacs-nox git glmark2 gnupg2 htop iputils-ping lsb-release mesa-utils nano psmisc sudo unzip vim wget

# Gazebo and related dependencies
RUN sh -c 'echo "deb http://packages.ros.org/ros/ubuntu `lsb_release -cs` main" > /etc/apt/sources.list.d/ros-latest.list'
RUN apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
RUN apt update
RUN apt install --no-install-recommends -y -qq coinor-libipopt-dev freeglut3-dev graphviz libassimp-dev libavcodec-dev libavdevice-dev libavformat-dev libavutil-dev libboost-all-dev libbullet-dev libccd-dev libcurl4-openssl-dev libeigen3-dev libfcl-dev libfreeimage-dev libgdal-dev libgts-dev libignition-tools-dev libjsoncpp-dev liboctomap-dev libode-dev libogre-1.9-dev libopenal-dev libprotobuf-dev libprotoc-dev libqwt-qt5-dev libqt5opengl5-dev libsimbody-dev libsqlite3-dev libswscale-dev libtinyxml2-dev libtar libtar-dev libtbb-dev liburdfdom-dev libxi-dev libxmu-dev libxslt1.1 libyaml-dev libzip-dev libzmq3-dev pkg-config protobuf-compiler python3-colcon-common-extensions python3-vcstool qt5-default ruby ruby-dev ruby-ffi swig xsltproc

# Create user with passwordless sudo
RUN useradd -l -G sudo -md /home/user -s /bin/bash -p user user
RUN sed -i.bkp -e 's/%sudo\s\+ALL=(ALL\(:ALL\)\?)\s\+ALL/%sudo ALL=NOPASSWD:ALL/g' /etc/sudoers

# Switch to user
USER user

# Dependencies (from source)
WORKDIR /home/user
RUN mkdir install
ENV PATH=${PATH}:/home/user/install/bin/
ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/home/user/install/lib/:/home/user/install/lib64/

RUN git clone https://github.com/stevengj/nlopt.git && \
    cd nlopt && \
    mkdir build && \
    cd build && \
    cmake .. -DCMAKE_INSTALL_PREFIX=/home/user/install && \
    make && \
    make install

RUN git clone https://github.com/esa/pagmo2.git && \
    cd pagmo2 && \
    mkdir build && \
    cd build && \
    cmake .. -DCMAKE_INSTALL_PREFIX=/home/user/install -DPAGMO_WITH_EIGEN3=ON -DPAGMO_WITH_NLOPT=ON -DPAGMO_WITH_IPOPT=ON && \
    make && \
    make install

RUN git clone https://github.com/dartsim/dart && \
    cd dart && \
    git checkout v6.11.1 && \
    mkdir build && \
    cd build && \
    cmake .. -DCMAKE_INSTALL_PREFIX=/home/user/install && \
    make && \
    make install

RUN mkdir /home/user/gazebo
WORKDIR /home/user/gazebo
RUN mkdir /home/user/gazebo/src && \
    wget https://raw.githubusercontent.com/ignition-tooling/gazebodistro/master/gazebo11.yaml
RUN vcs import src < gazebo11.yaml
RUN colcon metadata add default https://raw.githubusercontent.com/colcon/colcon-metadata-repository/master/index.yaml && \
    colcon metadata update
RUN MAKEFLAGS="-j 4" colcon build && \
    rm -r /home/user/dart/build && \
    rm -r /home/user/nlopt/build && \
    rm -r /home/user/pagmo2/build && \
    rm -r /home/user/gazebo/build

# Setup Gazebo11
RUN echo "source /home/user/gazebo/install/local_setup.bash" >> ~/.bashrc

# Launch bash from /home/user
WORKDIR /home/user
CMD ["bash"]
