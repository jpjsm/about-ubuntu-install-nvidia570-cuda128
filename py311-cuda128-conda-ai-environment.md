# PY 3.11 + CUDA 12.8 AI environment

## Check environment

- `nvidia-smi`
  - Should return something like:
    - Where driver should be **570.xxx.yy** or newer, and CUDA Version should be: **12.8** or newer

```txt
        Wed Nov 26 09:13:02 2025
        +-----------------------------------------------------------------------------------------+
        | NVIDIA-SMI 570.195.03             Driver Version: 570.195.03     CUDA Version: 12.8     |
        |-----------------------------------------+------------------------+----------------------+
        | GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
        | Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
        |                                         |                        |               MIG M. |
        |=========================================+========================+======================|
        |   0  NVIDIA GeForce RTX 4060 Ti     Off |   00000000:01:00.0  On |                  N/A |
        |  0%   40C    P8              8W /  165W |     518MiB /  16380MiB |      7%      Default |
        |                                         |                        |                  N/A |
        +-----------------------------------------+------------------------+----------------------+
                                                                                                
        +-----------------------------------------------------------------------------------------+
        | Processes:                                                                              |
        |  GPU   GI   CI              PID   Type   Process name                        GPU Memory |
        |        ID   ID                                                               Usage      |
        |=========================================================================================|
        |    0   N/A  N/A            2398      G   /usr/lib/xorg/Xorg                      271MiB |
        |    0   N/A  N/A            2677      G   /usr/bin/gnome-shell                     54MiB |
        |    0   N/A  N/A            3357      G   /usr/bin/gnome-text-editor               13MiB |
        |    0   N/A  N/A            8488      G   ...rack-uuid=3190708988185955192         33MiB |
        |    0   N/A  N/A           37847      G   /usr/pgadmin4/bin/pgadmin4               39MiB |
        |    0         nvcc: NVIDIA (R) Cuda compiler driver
        Copyright (c) 2005-2025 NVIDIA Corporation
        Built on Wed_Jan_15_19:20:09_PST_2025
        Cuda compilation tools, release 12.8, V12.8.61
        Build cuda_12.8.r12.8/compiler.35404655_0
  N/A  N/A           42524      G   /usr/share/code/code                     59MiB |
        +-----------------------------------------------------------------------------------------+
```
 
- `nvcc -V` 
  - Should return something like:
    - Where release should be **12.8**

```txt
        nvcc: NVIDIA (R) Cuda compiler driver
        Copyright (c) 2005-2025 NVIDIA Corporation
        Built on Wed_Jan_15_19:20:09_PST_2025
        Cuda compilation tools, release 12.8, V12.8.61
        Build cuda_12.8.r12.8/compiler.35404655_0
```

## create conda-forge environment

- Create an `environment.yml` file like the following
```txt
name: py311cu128ml
channels:
  - conda-forge
  - pytorch
  - nvidia
  - rapidsai
dependencies:
  - python=3.11
  - faiss-gpu=1.13.0
  - cupy
  - cudnn
  - cutensor
  - nccl
  - cuvs

variables:
  HF_HUB_DISABLE_PROGRESS_BARS: '1'
  HF_HUB_DISABLE_SYMLINKS_WARNING: '1'
  TF_ENABLE_ONEDNN_OPTS: '0'
  TRANSFORMERS_VERBOSITY: error
  HF_DATASETS_OFFLINE: '1'
  TRANSFORMERS_OFFLINE: '1'
```