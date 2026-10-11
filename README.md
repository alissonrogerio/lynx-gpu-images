# lynx-gpu-images

Prebuilt **generic** GPU runtime images for short GPU rentals, so a rented host can start working in seconds instead
of spending minutes installing CUDA Python stacks.

| image | contents |
|---|---|
| `ghcr.io/<owner>/lynx-wangp-runtime:cu128-torch2.10-wangp738fca1` | NVIDIA CUDA 12.8.1 + cuDNN runtime (Ubuntu 22.04), git/ffmpeg/rsync/build tools, uv, Python 3.11 venv at `/root/venv` with torch 2.10.0 (cu128) and the third-party PyPI packages listed in [WanGP](https://github.com/deepbeepmeep/Wan2GP)'s `requirements.txt` at commit `738fca1` |

What these images deliberately do **not** contain: WanGP's own source code (it has its own license and is cloned at job
start), model weights, credentials, or any private or production data.

Third-party components keep their own licenses: NVIDIA CUDA/cuDNN under the NVIDIA Deep Learning Container License,
PyTorch (BSD-3-Clause), and each PyPI package under its own license.
