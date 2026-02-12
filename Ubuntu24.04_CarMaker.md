# EC2 | Ubuntu 24.04 | CarMaker 15.0

## Install CarMaker

```bash
sudo apt update
sudo DEBIAN_FRONTEND=noninteractive apt install -y libxcb-cursor0 libusb-1.0-0-dev ttf-mscorefonts-installer

cd /tmp
aws s3 cp s3://carmaker-install/CarMakerOffice-linux-15.0.zip .
unzip ./CarMakerOffice-linux-15.0.zip
rm -f ./CarMakerOffice-linux-15.0.zip
cd /tmp/CarMakerOffice-linux-15.0
sudo ./ipg-install -batch ./*.tgz ./*.txz
echo "server 172.31.0.197" | sudo tee -a /opt/ipg/etc/Licenses
sed -i '$a export PATH=$PATH:/opt/ipg/bin' /home/ubuntu/.bashrc
sudo sed -i '/^exec /i export LD_PRELOAD=/lib/x86_64-linux-gnu/libstdc++.so.6' /opt/ipg/movienx/linux64-15.0/bin/MovieNX
sudo sed -i '/^exec /i export LD_PRELOAD=/lib/x86_64-linux-gnu/libstdc++.so.6' /opt/ipg/carmaker/linux64-15.0/bin/CM_Office
sudo sed -i '/^exec /i export LD_PRELOAD=/lib/x86_64-linux-gnu/libstdc++.so.6' /opt/ipg/carmaker/linux64-15.0/bin/CM_Office_Extended
sudo sed -i '/^exec /i export LD_PRELOAD=/lib/x86_64-linux-gnu/libstdc++.so.6' /opt/ipg/carmaker/linux64-15.0/bin/TM_Office
sudo sed -i '/^exec /i export LD_PRELOAD=/lib/x86_64-linux-gnu/libstdc++.so.6' /opt/ipg/carmaker/linux64-15.0/bin/TM_Office_Extended
sudo sed -i '/^exec /i export LD_PRELOAD=/lib/x86_64-linux-gnu/libstdc++.so.6' /opt/ipg/carmaker/linux64-15.0/bin/MM_Office
sudo sed -i '/^exec /i export LD_PRELOAD=/lib/x86_64-linux-gnu/libstdc++.so.6' /opt/ipg/carmaker/linux64-15.0/bin/MM_Office_Extended
```

## Clear bash history and shutdown

```bash
sudo shutdown -h +1
cat /dev/null > ~/.bash_history
history -c
```
