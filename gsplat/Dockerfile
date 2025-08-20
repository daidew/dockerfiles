# syntax=docker/dockerfile:1
FROM pytorch/pytorch:2.4.1-cuda12.1-cudnn9-devel

ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential git ffmpeg libgl1 libglib2.0-0 \
 && rm -rf /var/lib/apt/lists/*

# Toolchain & common deps
RUN pip install --upgrade pip setuptools wheel \
 && pip install ninja rich jaxtyping \
    imageio[ffmpeg] tqdm tyro viser pyyaml \
    torchmetrics lpips opencv-python-headless tensorboard nerfview

WORKDIR /workspace/gsplat
# Copy your repo root (must contain setup.py)
COPY gsplat/ /workspace/gsplat/

# *** KEY: set architectures explicitly; adjust to your GPUs as needed ***
# Examples:
#  - Ampere A100: 8.0
#  - Ampere RTX 30xx / A40: 8.6
#  - Ada (RTX 40/L40): 8.9
#  - Hopper (H100): 9.0
ARG TORCH_CUDA_ARCH_LIST="8.0;8.6;8.9;9.0"
ENV TORCH_CUDA_ARCH_LIST=${TORCH_CUDA_ARCH_LIST}

# Non-editable install so setup.py can import torch during build
RUN pip install . --no-build-isolation

# Examples dependencies (pycolmap, fused-ssim, etc.)
RUN pip install --no-build-isolation -r examples/requirements.txt

WORKDIR /workspace/gsplat/examples
EXPOSE 8080
CMD ["python", "simple_trainer.py", "--help"]
