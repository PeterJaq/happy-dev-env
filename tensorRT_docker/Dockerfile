FROM nvcr.io/nvidia/tensorrt:21.08-py3
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update && apt-get install -y \
    openssh-server \
    wget \
    vim \
    openssh-server \
    libopencv-dev \
    autoconf \
    automake \
    libtool \
    curl \
    make \
    g++ \
    gdb \
    gdbserver \
    unzip \
    sudo \
    && rm -rf /var/lib/apt/lists/*

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y pcl-tools libpcl-dev && rm -rf /var/lib/apt/lists/*


# install oh-my-zsh with awesome plugin
RUN sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.1.1/zsh-in-docker.sh)" -- \
    -t https://github.com/denysdovhan/spaceship-prompt \
    -p git \
    -p ssh-agent \
    -p extract \
    -p sudo \
    -p https://github.com/zsh-users/zsh-autosuggestions \
    -p https://github.com/zsh-users/zsh-completions \
    -p https://github.com/zsh-users/zsh-syntax-highlighting

# theme
# -t https://github.com/dracula/dracula-theme darchlar

# SSH
RUN mkdir -p /var/run/sshd

## Protobufv3.11.4

RUN git clone https://github.com/protocolbuffers/protobuf.git \
    && cd protobuf \
    && git checkout tags/v3.11.4 \
    && git submodule update --init --recursive \
    && ./autogen.sh \
    && ./configure \
    && make -j4 \
    && make install \
    && ldconfig

# authorize SSH connection with root account
RUN sed -ri 's/^#?PermitRootLogin\s+.*/PermitRootLogin yes/' /etc/ssh/sshd_config
# RUN sudo service ssh start
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

# change password root
RUN echo "root:123456"| chpasswd

ENTRYPOINT ["/entrypoint.sh"]

CMD ["zsh"]
