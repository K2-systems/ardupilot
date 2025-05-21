FROM ubuntu:22.04

ARG DEBIAN_FRONTEND=noninteractive
ARG USER_NAME=ardupilot
ARG USER_UID=1000
ARG USER_GID=1000

# Install dependencies
RUN apt-get update && apt-get install -y \
    lsb-release \
    sudo \
    tzdata \
    git \
    wget \
    python3 \
    python3-pip \
    python3-dev \
    build-essential \
    gdb \
    ccache \
    cmake \
    libopencv-dev \
    x11-apps \
    mesa-utils \
    xauth \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Create user with sudo privileges
RUN groupadd ${USER_NAME} --gid ${USER_GID} \
    && useradd -l -m ${USER_NAME} -u ${USER_UID} -g ${USER_GID} -s /bin/bash \
    && echo "${USER_NAME} ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/${USER_NAME} \
    && chmod 0440 /etc/sudoers.d/${USER_NAME}


# Copy the install script
COPY Tools/ /tmp/Tools/
RUN chmod +x /tmp/Tools/environment_install/install-prereqs-ubuntu.sh

# Run the install script as the non-root user
RUN su - ${USER_NAME} -c "SKIP_AP_GRAPHIC_ENV=0 SKIP_AP_COV_ENV=1 SKIP_AP_GIT_CHECK=1 /tmp/Tools/environment_install/install-prereqs-ubuntu.sh -y"

# Install additional packages for XPlane connectivity
RUN apt-get update && apt-get install -y \
    net-tools \
    iproute2 \
    iputils-ping \
    wireless-tools \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

USER ${USER_NAME}
WORKDIR /home/${USER_NAME}

# Set up environment variables
RUN echo "export PATH=/home/${USER_NAME}/.local/bin:\$PATH" >> /home/${USER_NAME}/.bashrc \
    && echo "export DISPLAY=:0" >> /home/${USER_NAME}/.bashrc \
    && echo "export PULSE_SERVER=unix:/tmp/pulse-socket" >> /home/${USER_NAME}/.bashrc

CMD ["/bin/bash"]