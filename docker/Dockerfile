FROM ros:kinetic

# Install apt-fast
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update \
  && apt-get install --no-install-recommends -y software-properties-common gnupg ca-certificates \
  && add-apt-repository ppa:apt-fast/stable \
  && apt-get update \
  && apt-get install --no-install-recommends -y apt-fast \
  && apt-fast clean \
  && rm -rf /var/lib/apt/lists/*

RUN echo debconf apt-fast/maxdownloads string 16 | debconf-set-selections && \
  echo debconf apt-fast/dlflag boolean true | debconf-set-selections && \
  echo debconf apt-fast/aptmanager string apt-get | debconf-set-selections

RUN apt-fast update \
  && apt-fast install -y --no-install-recommends \
  git wget nano libomp-dev build-essential python3-pip \
  libopencv-dev
RUN apt-fast install -y --no-install-recommends \
  ros-${ROS_DISTRO}-eigen-conversions ros-${ROS_DISTRO}-cv-bridge \
  ros-${ROS_DISTRO}-image-transport-plugins \
  ros-${ROS_DISTRO}-pcl-ros ros-${ROS_DISTRO}-rviz ros-${ROS_DISTRO}-octomap-ros \
  ros-${ROS_DISTRO}-octomap-server
# && apt-get clean && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /root/catkin_ws/src
WORKDIR /root/catkin_ws/src
RUN /bin/bash -c '. /opt/ros/${ROS_DISTRO}/setup.bash; catkin_init_workspace'

COPY . /root/catkin_ws/src/amcl3d

WORKDIR /root/catkin_ws
RUN /bin/bash -c '. /opt/ros/${ROS_DISTRO}/setup.bash; catkin_make -DCMAKE_BUILD_TYPE=Release'

RUN sed -i "6i source \"/root/catkin_ws/devel/setup.bash\"" /ros_entrypoint.sh

WORKDIR /

ENTRYPOINT ["/ros_entrypoint.sh"]
CMD ["bash"]
