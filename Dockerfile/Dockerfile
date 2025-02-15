FROM ubuntu:20.04

ENV DEBIAN_FRONTEND noninteractive
ENV IGNITION_VERSION fortress

# Make sure everything is up to date before building from source
RUN apt-get update \
  && apt-get upgrade -y \
  && apt-get update && apt-get install -q -y --no-install-recommends \
    dirmngr \
    gnupg \
    lsb-release \
    wget \
    curl \
    git \
    ca-certificates \
    build-essential \
    cmake \
  && apt-get clean

RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg && \
  echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | tee /etc/apt/sources.list.d/ros2.list > /dev/null && \
  apt-get update && apt-get install -q -y --no-install-recommends \
    python3-colcon-ros \
    python3-colcon-common-extensions \
    python3-rosdep \
    && apt-get clean

RUN mkdir -p /home/ros2_ws/src \
    && cd /home/ros2_ws/src \
    && git clone https://github.com/ignitionrobotics/ign_ros2_control/ \
    && rosdep init && rosdep update \
    && rosdep install --from-paths ./ -i -y --rosdistro rolling

RUN cd /home/ros2_ws/ \
  && . /opt/ros/rolling/setup.sh \
  && colcon build --merge-install

COPY entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

CMD ros2 launch ign_ros2_control_demos cart_example_position.launch.py
