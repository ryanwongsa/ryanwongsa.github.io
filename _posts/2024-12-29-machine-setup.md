---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Cheatsheet for Setting up Local Machine"
subtitle: "General tips on the process to set up a local machine for deep learning"
summary: ""
authors: [admin]
tags: []
categories: []
permalink: /post/local-machine-instructions/
date: 2023-12-16T07:45:08+01:00
lastmod: 2023-12-16T07:45:08+01:00
featured: false
draft: false
 
# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
# image:
#   caption: ""
#   focal_point: Smart
#   preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

# VM Setup Notes

General notes / cheatsheet on how to set up local environments on a new computer.

Enter the following commands:

```bash
sudo apt update
sudo apt -y upgrade
sudo apt -y dist-upgrade
sudo apt -y autoremove
sudo apt clean
sudo reboot now
```

## Firmware update

```bash
sudo fwupdmgr get-upgrades
sudo fwupdmgr upgrade
```

## CUDA + cuDNN

Assuming Ubuntu 20.04

```bash
OS=ubuntu2004
wget https://developer.download.nvidia.com/compute/cuda/repos/${OS}/x86_64/cuda-keyring_1.0-1_all.deb
sudo dpkg -i cuda-keyring_1.0-1_all.deb && rm cuda-keyring_1.0-1_all.deb
sudo apt update
sudo apt -y install cuda
sudo apt -y install libcudnn8 libcudnn8-dev
```

## Docker/NVIDIA Docker

```bash
sudo apt -y remove docker docker-engine docker.io containerd runc
sudo apt update
sudo apt -y install apt-transport-https ca-certificates curl gnupg lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt -y install docker-ce docker-ce-cli containerd.io
sudo docker run hello-world
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
docker run hello-world

distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
&& curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add - \
&& curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt update
sudo apt -y install nvidia-docker2
sudo systemctl restart docker
```

```bash
docker run --rm --gpus all nvidia/cuda:11.0-base nvidia-smi
```