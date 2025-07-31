FROM mpsfm/mpsfm:latest

ENV DEBIAN_FRONTEND noninteractive \
    TZ=Asia/Bangkok
RUN apt-get update && \ 
    apt-get install -y --no-install-recommends \
    zip \
    unzip \
    wget \
    curl \
    ffmpeg \
    libsm6 \
    libxext6 \
    git-all \
    ssh
RUN python3 -m pip install --upgrade pip && \
    python3 -m pip install --upgrade pyproject jupyter jupyterlab h5py tqdm matplotlib ipywidgets kfp

WORKDIR /

# remove existing folder that contains only requirements.txt
RUN rm -rf /mpsfm

ADD mpsfm/ /mpsfm/

# install mpsfm, depth pro and (optional) MASt3R speed up
RUN cd /mpsfm && \
    pip install -e . 

# aws CLI
RUN cd / && \
    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" && \
    unzip awscliv2.zip && \
    ./aws/install

# jupyterlab for kf
EXPOSE 8888
ENV NB_PREFIX /
ENV SHELL=/bin/bash
CMD ["-c", "jupyter lab --notebook-dir=/home/jovyan --ip=0.0.0.0 --allow-root --port=8888 --NotebookApp.token='' --NotebookApp.password='' --NotebookApp.allow_origin='*' --NotebookApp.base_url=${NB_PREFIX} --NotebookApp.iopub_data_rate_limit=1.0e10"]
