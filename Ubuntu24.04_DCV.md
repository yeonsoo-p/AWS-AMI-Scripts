# EC2 | Ubuntu 24.04 | NVIDIA GRID | Amazon DCV

## Install DKMS and kernel headers

```bash
sudo apt update
sudo apt upgrade -y
sudo apt autoremove -y
sudo apt install -y dkms linux-headers-aws linux-modules-extra-aws unzip gcc make libglvnd-dev pkg-config
sudo apt install -y mesa-utils vulkan-tools clinfo amazon-ec2-utils

sudo tee /etc/modprobe.d/blacklist.conf > /dev/null << EOF
blacklist vga16fb
blacklist nouveau
blacklist rivafb
blacklist nvidiafb
blacklist rivatv
EOF

sudo sed -i "s/^GRUB_CMDLINE_LINUX=\"/&rdblacklist=nouveau/" /etc/default/grub
sudo update-grub
sudo reboot
```

## Install GNOME desktop and Amazon DCV server

```bash
sudo apt update
sudo apt install -y ubuntu-desktop-minimal
sudo sed -i '/^\[daemon\]/a WaylandEnable=false' /etc/gdm3/custom.conf
sudo systemctl set-default graphical.target
sudo sed -i "s/Prompt=lts/Prompt=never/g" /etc/update-manager/release-upgrades

cd /tmp
OS_VERSION=$(. /etc/os-release;echo $VERSION_ID | sed -e 's/\.//g')
curl -L -O https://d1uj6qtbmh3dt5.cloudfront.net/nice-dcv-ubuntu$OS_VERSION-$(arch).tgz
tar -xvzf nice-dcv-ubuntu$OS_VERSION-$(arch).tgz && cd nice-dcv-*-$(arch)
sudo apt install -y ./nice-dcv-server_*.deb
sudo apt install -y ./nice-dcv-web-viewer_*.deb
sudo apt install -y ./nice-xdcv_*.deb
sudo apt install -y ./nice-dcv-gltest_*.deb
sudo usermod -aG video dcv
sudo systemctl enable dcvserver

sudo sed -i "/^\[session-management\/automatic-console-session/a owner=\"ubuntu\"\nstorage-root=\"%home%\"" /etc/dcv/dcv.conf
sudo sed -i "s/^#create-session/create-session/g" /etc/dcv/dcv.conf

sudo apt install -y cups
sudo usermod -a -G lpadmin dcv
sudo systemctl enable --now cups

gsettings set org.gnome.desktop.session idle-delay 0
gsettings set org.gnome.desktop.screensaver lock-enabled false
gsettings set org.gnome.desktop.screensaver idle-activation-enabled false
```

## Install NVIDIA GRID driver

```bash
cd /tmp

if (lsb_release -r -s | grep -q 22); then
  if (cat /proc/version | grep -q gcc-12); then
    sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-12 12
    sudo update-alternatives --config gcc
  fi
fi

sudo snap install aws-cli --classic
aws s3 cp --recursive s3://ec2-linux-nvidia-drivers/latest/ . --no-sign-request
chmod +x NVIDIA-Linux-x86_64*.run
sudo ./NVIDIA-Linux-x86_64*.run -s

echo "options nvidia NVreg_EnableGpuFirmware=0" | sudo tee --append /etc/modprobe.d/nvidia.conf

sudo nvidia-xconfig --enable-all-gpus --connected-monitor=DFP-0,DFP-1,DFP-2,DFP-3
```

## Configure password

```bash
sudo sed -i 's/lock_passwd: True/lock_passwd: False/' /etc/cloud/cloud.cfg
sudo passwd ubuntu
```

## Clear bash history and shutdown

```bash
sudo shutdown -h +1
cat /dev/null > ~/.bash_history
history -c
```
