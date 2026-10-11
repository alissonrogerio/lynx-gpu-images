# LYNX GPU runtime for WanGP-based jobs — GENERIC components only.
# Contains: NVIDIA CUDA 12.8.1 minimal base (torch cu128 wheels ship their own CUDA/cuDNN libs), apt tools (git, ffmpeg, rsync, build tools), uv, a Python 3.11 venv
# with torch 2.10.0 (cu128) and the third-party PyPI packages listed in WanGP's requirements.txt at the pinned commit.
# Does NOT contain: WanGP's own source (custom license; cloned at job start), model weights, credentials, or any
# private/production data.
FROM nvidia/cuda:12.8.1-base-ubuntu22.04

ARG UV_VERSION=0.12.18
ARG WANGP_SHA=738fca1a05b7a39ea9d4bc5ac15989893d30a13e
ENV DEBIAN_FRONTEND=noninteractive PIP_NO_CACHE_DIR=1 UV_NO_CACHE=1 UV_HTTP_TIMEOUT=300 \
    VIRTUAL_ENV=/root/venv PATH=/root/venv/bin:/usr/local/bin:$PATH \
    HF_HUB_ENABLE_HF_TRANSFER=1

RUN apt-get update && apt-get install -y --no-install-recommends \
        git ffmpeg build-essential libgl1 libglib2.0-0 curl ca-certificates rsync openssh-server python3 \
    && rm -rf /var/lib/apt/lists/*

RUN curl -LsSf "https://github.com/astral-sh/uv/releases/download/${UV_VERSION}/uv-x86_64-unknown-linux-gnu.tar.gz" \
      | tar -xz --strip-components=1 -C /usr/local/bin \
    && uv venv --python 3.11 /root/venv

# layer 1: torch (largest, changes least)
RUN uv pip install --python /root/venv torch==2.10.0 torchvision==0.25.0 torchaudio==2.10.0 \
      --index-url https://download.pytorch.org/whl/cu128

# layer 2: third-party requirements of WanGP at the pinned commit (the list only; WanGP code is NOT copied)
RUN curl -fsSL "https://raw.githubusercontent.com/deepbeepmeep/Wan2GP/${WANGP_SHA}/requirements.txt" -o /tmp/wangp-requirements.txt \
    && uv pip install --python /root/venv --index-strategy unsafe-best-match -r /tmp/wangp-requirements.txt \
         "huggingface_hub[hf_transfer]" setuptools \
    && rm -f /tmp/wangp-requirements.txt

RUN printf 'image=lynx-wangp-runtime\nwangp_requirements_sha=%s\ntorch=2.10.0+cu128\n' "$WANGP_SHA" > /etc/lynx-image \
    && /root/venv/bin/python -c "import torch, torchvision, diffusers, transformers; print('imports ok', torch.__version__)"

CMD ["bash"]
