FROM nvidia/cuda:12.6.1-base-ubuntu22.04

ENV DEBIAN_FRONTEND=noninteractive
ENV LANG C.UTF-8
ARG PYTHON_VERSION=python3.12

# Install utilities
RUN set -xe && \
    apt-get update && \
    apt-get install -y --no-install-recommends \
        gnupg \
        ca-certificates \
        wget \
        vim \
        tmux \
        git \
        build-essential \
        pkg-config \
        curl \
        software-properties-common \
        unzip \
        libmagic-dev \
        tzdata \
        cron && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Install CUDA packages
RUN wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-keyring_1.1-1_all.deb && \
    dpkg -i cuda-keyring_1.1-1_all.deb && \
    apt-get update && \
    apt-get -y install cuda-toolkit-12-6

# Install python and packages
RUN set -xe && \
    add-apt-repository ppa:deadsnakes/ppa -y && \
    apt-get update && \
    apt-get install -y --no-install-recommends \
        $PYTHON_VERSION \
        $PYTHON_VERSION-dev \
        $PYTHON_VERSION-venv \
        $PYTHON_VERSION-distutils && \
    rm -f /usr/bin/python && \
    ln -s /usr/bin/$PYTHON_VERSION /usr/bin/python && \
    rm -f /usr/bin/python3 && \
    ln -s /usr/bin/$PYTHON_VERSION /usr/bin/python3 && \
    curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py && \
    python get-pip.py && \
    rm get-pip.py && \
    pip install --no-cache-dir --upgrade pip && \
    python -m pip install --no-cache-dir \
        numpy \
        setproctitle \
        tzdata \
        jupyter \
        pandas \
        scipy \
        scikit-learn \
        seaborn \
        matplotlib && \
    python -m pip install --no-cache-dir \
        torch==2.4.0 \
        torchvision==0.19.0 \
        torchaudio==2.4.0 \
        --index-url https://download.pytorch.org/whl/cu121 && \
    python -m pip install "unsloth[cu121-torch240] @ git+https://github.com/unslothai/unsloth.git" && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Clean up unnecessary static libraries and configure dynamic linker
RUN find /usr/local/cuda-*/lib*/ -type f -name 'lib*_static.a' -not -name 'libcudart_static.a' -delete && \
    rm -f /usr/lib/x86_64-linux-gnu/libcudnn_static_v*.a && \
    ln -s /usr/local/cuda/lib64/stubs/libcuda.so /usr/local/cuda/lib64/stubs/libcuda.so.1 && \
    echo "/usr/local/cuda/lib64/stubs" > /etc/ld.so.conf.d/z-cuda-stubs.conf && \
    ldconfig

# Install vscode server
RUN curl -fsSL https://code-server.dev/install.sh | sh && \
    mkdir -p ~/.config/code-server

# Set time zone
RUN ln -fs /usr/share/zoneinfo/Asia/Seoul /etc/localtime && \
    echo "Asia/Seoul" > /etc/timezone

WORKDIR /workspace

CMD ["bash"]



# Build Dockerfile and run container
# docker build . --tag test_llama:3.1b
# docker run -it --rm --gpus all --ipc=host --name test_llama test_llama:3.1b bash
