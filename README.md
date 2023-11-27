# onnx-raspberry-pi-zero-wheel
ONNX wheel compiled on a Raspberry Pi Zero

## Background

After trying many methods with Docker, pip, CMake, etc., to get any version of ONNX compiling on a RPi Zero, which runs `armv6l`, finally the only thing that worked was cloning the repo and building the wheel on device.

This repo contains the compiled wheel, as well as the commands run on device to create it.

## Device info used to compile the wheel

```bash
liam@raspberrypi:~ $ uname -a
Linux raspberrypi 6.1.21+ #1642 Mon Apr  3 17:19:14 BST 2023 armv6l GNU/Linux
```

```bash
liam@raspberrypi:~ $ cat /etc/os-release
PRETTY_NAME="Raspbian GNU/Linux 11 (bullseye)"
NAME="Raspbian GNU/Linux"
VERSION_ID="11"
VERSION="11 (bullseye)"
VERSION_CODENAME=bullseye
ID=raspbian
ID_LIKE=debian
HOME_URL="http://www.raspbian.org/"
SUPPORT_URL="http://www.raspbian.org/RaspbianForums"
BUG_REPORT_URL="http://www.raspbian.org/RaspbianBugs"
```

## Commands run on the device to reproduce

```bash
# Clone the repo
git clone --single-branch --branch main --recursive https://github.com/onnx/onnx
cd onnx/

# Create a 3GB swap file and enable it
dd if=/dev/zero of=/tmp/swapfile.img bs=1024 count=3M
sudo chmod 0600 /tmp/swapfile.img
sudo chown root /tmp/swapfile.img
sudo mkswap /tmp/swapfile.img
sudo swapon /tmp/swapfile.img

# Upgrade pip and setuptools
pip3 install --upgrade pip setuptools

# Build the wheel
python3 setup.py bdist_wheel

# Done! Clean up by disabling and removing the swapfile we createdL
sudo swapoff /tmp/swapfile.img
sudo rm /tmp/swapfile.img

# And if desired, install the wheel with pip:
pip3 install dist/onnx-1.16.0-cp39-cp39-linux_armv6l.whl
```
