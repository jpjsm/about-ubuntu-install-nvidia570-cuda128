# NVIDIA 570 driver install and CUDA 12.8 toolkit

## Driver Install

```bash
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
sudo apt install nvidia-driver-570
sudo apt-mark hold nvidia-driver-570
```

***Reboot***

## Install CUDA Toolkit 12.8

```bash 
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-ubuntu2404.pin
sudo mv cuda-ubuntu2404.pin /etc/apt/preferences.d/cuda-repository-pin-600

wget https://developer.download.nvidia.com/compute/cuda/12.8.0/local_installers/cuda-repo-ubuntu2404-12-8-local_12.8.0-570.86.10-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu2404-12-8-local_12.8.0-570.86.10-1_amd64.deb

sudo cp /var/cuda-repo-ubuntu2404-12-8-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update

sudo apt-get -y install cuda-toolkit-12-8
sudo apt-mark hold cuda-toolkit-12-8
```

## Make CUDA 12.8 available

- Add to `.bashrc`

```txt
    export PATH=${PATH}:/usr/local/cuda-12.8/bin
    export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/cuda-12.8/lib64
```

***Reboot***

## Install cuDNN

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt-get -y install cudnn9-cuda-12

sudo apt -y autoremove
sudo apt-mark hold cudnn9-cuda-12
```

### Check cuDNN

```bash
sudo apt-get -y install libcudnn9-samples
sudo apt-get - install libfreeimage3 libfreeimage-dev
mkdir -p ~/samples/cudnn_samples_v9/mnistCUDNN
cp -v /usr/src/cudnn_samples_v9/mnistCUDNN/* $HOME/samples/cudnn_samples_v9/mnistCUDNN
cd $HOME/samples/cudnn_samples_v9/mnistCUDNN
make clean && make
./mnistCUDNN
```

## Hold packages

`sudo apt-mark hold $(dpkg -l | egrep -i 'cuda|nvidia|cudnn9' | egrep 'meta-?package' | awk '{print $2 }')`
