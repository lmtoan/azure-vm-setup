# Azure GPU VM Setup

Scripts to set up Azure GPU VM that install all prerequisites for latest Tensorflow on **Ubuntu 18.04**:
- CUDA 10 drivers
- CUDNN 7.4.1.5
- TensorRT
- pip
- virtualenv

Instructions
---
In `azure-vm-setup`, run:
- `bash cuda_setup_part1.sh` to install NVIDIA drivers. This will take about 10 minutes and there will be a reboot.
- Login to the VM again and run `nvidia-smi` to make sure CUDA driver is installed
- `bash cuda_setup_part2.sh` to install CU-DNN. This will take about 20 minutes and there will be a reboot.
- Login to the VM again and run `nvcc --version`. Should show up as CUDA 10.1.
- Run `bash test_cuda.sh` to run CUDNN tests. Should show up as successful.

Run `pip list --format=columns` to check all base packages are there.

Run `virtualenv --help` to check if virtualenv is there.

**STOP HERE. DO NOT PIP INSTALL ANYTHING ELSE**

Source: https://www.tensorflow.org/install/gpu#ubuntu_1804_cuda_10
