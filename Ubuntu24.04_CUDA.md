# EC2 | Ubuntu 24.04 | CUDA

## Install CUDA

```bash
cd /tmp

wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt update
sudo apt -y install cuda-toolkit-13-0
sed -i '$a export PATH=$PATH:/usr/local/cuda/bin' /home/ubuntu/.bashrc
sed -i '$a export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/lib64' /home/ubuntu/.bashrc
```

## Clear bash history and shutdown

```bash
sudo shutdown -h +1
cat /dev/null > ~/.bash_history
history -c
```
