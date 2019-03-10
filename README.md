# Azure GPU VM Setup

Scripts to set up Azure GPU VM that install all prerequisites for latest Tensorflow on **Ubuntu 18.04**:
- CUDA 10 drivers
- CUDNN 7.4.1.5
- TensorRT
- pip
- virtualenv

Optional:
- Docker CE
- nvidia-docker 2.0

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

Virtual Environment Setup
---
Steps:
- `mkdir /datadrive/envs`
- `virtualenv --python=python3.6 /datadrive/envs/<your_env_name> --system-site-packages`. 
	- This command will create a Python 3 virtual environment that inherits all the base packages that are pre-installed in the VM. The virtual environment is stored on /datadrive/envs directory to save OS disk space.
- To activate your environment, do `source /datadrive/envs/<your_env_name>/bin/activate`
	- If fancy, add this line to `.bashrc` : `alias <your_env_name>='source /datadrive/envs/<your_env_name>/bin/activate'`. Then `source ~/.bashrc` to take effect
	- After that, only run `your-env-name` to activate the environment and begin work
- To test Tensorflow-GPU, **inside your virtual environment**, run:
	- `pip install --upgrade tensorflow-gpu`. *Do not pip install tensorflow as to not confuse with CPU version*
	- In python, run `import tensorflow as tf` and `tf.test.gpu_device_name()`. If CUDA device shows up, congratulations!

**AGAIN, DO NOT PIP INSTALL ANYTHING OUTSIDE VIRTUAL ENVIRONMENT**. If you think your virtual environment is messed up, delete the folder /datadrive/envs/your_env_name and repeat the process.

Jupyter Notebook Setup
---
1) Open port for public access. Follow [this](https://github.com/rgl/azure-content/blob/master/articles/virtual-machines/virtual-machines-linux-jupyter-notebook.md#create-a-linux-vm-and-open-a-port-for-jupyter)

2) Jupyter server setup:
- **Inside your virtual environment**, run `pip install jupyter`.
- Generate a default Jupyter config in home directory by `jupyter notebook --generate-config`
- Run `jupyter notebook password` and enter your password
- Run `cat /home/your-user-name/.jupyter/jupyter_notebook_config.json` and copy the password starting with 'sha1:...' somewhere else
- Open the config file with `nano /home/your-user-name/.jupyter/jupyter_notebook_config.py` and enter the following lines
	- Change **your-user-name**, **your-sha1-password** that you copied somewhere else, and **desired-port** which is whichever port you opened from step 1)

```
c = get_config()

# You must give the path to the certificate file.
c.NotebookApp.certfile = u'/home/your-user-name/.jupyter/mycert.pem'

# Create your own password as indicated above
c.NotebookApp.password = u'your-sha1-password'

# Network and browser details. We use a fixed port (9999) so it matches
# our Azure setup, where we've allowed traffic on that port
c.NotebookApp.ip = 0.0.0.0
c.NotebookApp.port = desired-port
c.NotebookApp.open_browser = False
```

Docker Setup
---
Inside `azure-vm-setup` directory:
- Run `bash docker.sh` to install Docker CE
- Run `bash nvidia_docker.sh` to install Nvidia-Docker


Source: 
- https://www.tensorflow.org/install/gpu#ubuntu_1804_cuda_10
- https://github.com/NVIDIA/nvidia-docker/wiki/Installation-(version-2.0)
