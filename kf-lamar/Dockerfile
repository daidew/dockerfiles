FROM graffitytech/colmap:3.8-cuda11.7.0-devel-ubuntu22.04

ENV DEBIAN_FRONTEND noninteractive \
    TZ=Asia/Bangkok
    
WORKDIR /

RUN apt-get update -y && apt-get install -y --no-install-recommends \
    zip \
    unzip \
    wget \
    curl \
    ffmpeg \
    libsm6 \
    libxext6 \
    git-all \
    ssh \
    tmux \
    htop \
    cmake libgoogle-glog-dev libgflags-dev libatlas-base-dev libeigen3-dev \
    libsuitesparse-dev build-essential

# AWS CLI
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
RUN unzip awscliv2.zip
RUN ./aws/install

RUN git clone https://github.com/ceres-solver/ceres-solver.git && \
    cd ceres-solver && \
    git checkout tags/2.1.0 && \
    mkdir build && \
    cd build && \
    cmake .. && \
    make -j8 && \
    make install

COPY requirements.txt requirements.txt

RUN apt update && \
    apt install -y software-properties-common && \
    add-apt-repository -y ppa:deadsnakes/ppa && \
    apt install python3.10 python3.10-dev python3.10-distutils -y

RUN curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py && \
    python3 get-pip.py

RUN apt-get update -y && apt-get install -y --no-install-recommends libyaml-dev

ENV CUDAARCHS=75

RUN python3 -m pip install --upgrade pip setuptools wheel && \
    python3 -m pip install "Cython<3.0.0" && \
    python3 -m pip install pyyaml==5.4.1 --no-build-isolation && \
    python3 -m pip install --ignore-installed blinker==1.6.3 && \
    python3 -m pip install --no-cache-dir -r requirements.txt --break-system-packages

RUN git clone --recursive https://github.com/cvg/Hierarchical-Localization/ && \
    cd Hierarchical-Localization/ && \
    python3 -m pip install -e .

# our lamar-benchmark (for PGO) requires pycolmap==0.6.0 but hloc (at the current date) now requires >=3.12.3 (need to force it)
# I think usually PGO and hloc are in a separate container, so this is fine, but we need to overwrite it to make it work in a single container.
RUN python3 -m pip install pycolmap==0.6.0

RUN python3 -m pip install --upgrade pip && \
    python3 -m pip install --upgrade pyproject jupyter jupyterlab h5py tqdm matplotlib ipywidgets kfp

EXPOSE 8888
ENV NB_PREFIX /
ENV SHELL=/bin/bash
CMD ["sh","-c", "jupyter lab --notebook-dir=/home/jovyan --ip=0.0.0.0 --allow-root --port=8888 --NotebookApp.token='' --NotebookApp.password='' --NotebookApp.allow_origin='*' --NotebookApp.base_url=${NB_PREFIX} --NotebookApp.iopub_data_rate_limit=1.0e10"]
