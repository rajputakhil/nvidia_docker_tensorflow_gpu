# NVIDIA Container Runtime for Docker

This tutorial will help users to use Nvidia Docker image (tensorflow-gpu) on Ubuntu with a machine having GTX 1080ti Nvidia Graphic Card.

## 1. Install Ubuntu 18.04

## 2. Install NVIDIA driver - Proprietary GPU Driver

https://www.mvps.net/docs/install-nvidia-drivers-ubuntu-18-04-lts-bionic-beaver-linux/

### i – Clean the system of other Nvidia drivers

Before we start installing the correct driver, we need to clean the system of any previously installed driver that might create software issues.

We can do that by using the following command:

    $ sudo apt-get purge nvidia*

### ii – Check the latest driver version for our Nvidia GPU

We need to check what version of the latest drivers is our graphic card capable of running.

We can check this by visiting the following page and see what is the correct driver version for our graphic card.

https://www.nvidia.com/object/unix.html

Afterwards, we will visit the graphic driver PPA homepage here (https://launchpad.net/~graphics-drivers/+archive/ubuntu/ppa) and see if our graphic card is compatible with the drivers present in the repository.

### iii – Add the Nvidia graphic card PPA

We can add the graphic-driver PPA using the following command:

    $ sudo add-apt-repository ppa:graphics-drivers

### iv - Prepare the system for the installation

To retrieve information about the latest version of the packages listed in the repository we run:

    $ sudo apt-get update

### v – Install the Nvidia GPU driver

Using the previously aquired information from step 2, we download and install the latest Nvidia driver supported by our GPU. Please note that the command is different for each graphic card, depending on the driver available for it:

    $ sudo apt-get install nvidia-390

( Ex. nvidia-390 is the latest driver version for the GTX 1xxx series).

After the installation is done, we need to reboot the computer:

    $ sudo reboot

### vi – Verify Nvidia Driver installation

After the system has resumed, we can check if the driver has been installed correctly:

    $ lsmod | grep nvidia

or

    $ nvidia-smi

If there is no output, most likely your your driver install process has failed. It is also possible that the GPU driver is not available in your system driver database. You can check if your system is running on the open source driver nouveau. If there is no output for nouveau, then your installation has succeded.

    $ lsmod | grep nouveau

## 3. Install Docker

## 4. Install nvidia-docker2

(https://github.com/NVIDIA/nvidia-docker/blob/master/README.md)

#### If you have nvidia-docker 1.0 installed: we need to remove it and all existing GPU containers
    $ docker volume ls -q -f driver=nvidia-docker | xargs -r -I{} -n1 docker ps -q -a -f volume={} | xargs -r docker rm -f
    $ sudo apt-get purge -y nvidia-docker

#### Add the package repositories
    $ curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \
    sudo apt-key add -
    distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
    curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
    sudo tee /etc/apt/sources.list.d/nvidia-docker.list
    $ sudo apt-get update

#### Install nvidia-docker2 and reload the Docker daemon configuration
    $ sudo apt-get install -y nvidia-docker2
    $ sudo pkill -SIGHUP dockerd

#### Test nvidia-smi with the latest official CUDA image
    $ docker run --runtime=nvidia --rm nvidia/cuda:9.0-base nvidia-smi

## 5. Install Tensorflow Nvidia-container image with Jupyter Notebook functionality

a. Create a 'Dockerfile'

```docker
FROM nvcr.io/nvidia/tensorflow:18.08-py3
WORKDIR /my-ml-files
RUN pip install jupyter
EXPOSE 8888
RUN pip install keras
```

b. Run the following in a terminal inside of the folder where you saved the "Dockerfile"

```sh
docker build -t my-nvidia-container .
```

c. The container is now built. To run it run the following

```sh
sudo nvidia-docker run --runtime=nvidia -it --rm -p "8888:8888" -v /home/user/:/my-ml-files my-nvidia-tensorflow-18.08
```

d. When you're inside of the docker container run 

```sh
jupyter notebook --port=8888 --ip=0.0.0.0 --allow-root --no-browser .
```
e. Launch the Notebook

```sh
localhost:8888 and the token
```

f. Run the following in a Notebook to find out if GPU is working

```py
import tensorflow as tf
tf.test.gpu_device_name()
```

g. Which GPU am I Using?

```py
from tensorflow.python.client import device_lib
device_lib.list_local_devices()
```

h. CPU and GPU

```sh
nvidia-smi
```
## You can work with Tensorflow Notebooks employing the Nvidia GPU