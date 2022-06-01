ARG PYTORCH="1.11.0"
ARG CUDA="11.3"
ARG CUDNN="8"
ARG MMCV="1.5.0"
ARG MMDET="2.19.0"
ARG MMSEG="0.20.0"
FROM pytorch/pytorch:${PYTORCH}-cuda${CUDA}-cudnn${CUDNN}-devel

ENV TORCH_CUDA_ARCH_LIST="6.0 6.1 7.0+PTX"
ENV TORCH_NVCC_FLAGS="-Xfatbin -compress-all"
ENV CMAKE_PREFIX_PATH="$(dirname $(which conda))/../"

# To fix GPG key error when running apt-get update
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/3bf863cc.pub
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/7fa2af80.pub

RUN apt-get update && apt-get install -y ffmpeg libsm6 libxext6 git ninja-build libglib2.0-0 libsm6 libxrender-dev libxext6 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y \
    openssh-server \
    wget \
    vim \
    openssh-server \
    && rm -rf /var/lib/apt/lists/*

# install oh-my-zsh with awesome plugin
RUN yes | sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.1.1/zsh-in-docker.sh)" -- \
    -t https://github.com/denysdovhan/spaceship-prompt \
    -p git \
    -p ssh-agent \
    -p extract \
    -p sudo \
    -p https://github.com/zsh-users/zsh-autosuggestions \
    -p https://github.com/zsh-users/zsh-completions \
    -p https://github.com/zsh-users/zsh-syntax-highlighting


# Install MMCV, MMDetection and MMSegmentation
ARG PYTORCH
ARG CUDA
ARG MMCV
ARG MMDET
ARG MMSEG
RUN ["/bin/bash", "-c", "pip install --no-cache-dir mmcv-full==${MMCV} -f https://download.openmmlab.com/mmcv/dist/cu${CUDA//./}/torch${PYTORCH}/index.html"]
RUN pip install --no-cache-dir mmdet==${MMDET} mmsegmentation==${MMSEG}

# Install MMDetection3D
# RUN conda clean --all
# COPY . /mmdetection3d
# WORKDIR /mmdetection3d
# ENV FORCE_CUDA="1"
# RUN pip install -r requirements/build.txt
# RUN pip install --no-cache-dir -e .

COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]

CMD ["zsh"]