---
layout: post
title: Ollama and AMD 780m GPU
date: 2025-02-25
author: luka
categories:
- linux
---

Ollama is a cool tool for running LLMs locally. However, it requires significant computational resources to function properly. I've had some issues when configuring it to run over GPU for my AMD 780M graphics card.

It turned out to be a fairly simple thing to update.

You need rocm and AMD drivers installed. I have version 6.3.3 At the time of writing, this is the newest version. 

Instructions on [AMD page](https://rocm.docs.amd.com/projects/install-on-linux/en/latest/install/quick-start.html) copied here for reference (ubuntu 24.04):
 

```sh
sudo apt update
sudo apt install "linux-headers-$(uname -r)" "linux-modules-extra-$(uname -r)"
sudo apt install python3-setuptools python3-wheel
sudo usermod -a -G render,video $LOGNAME # Add the current user to the render and video groups
wget https://repo.radeon.com/amdgpu-install/6.3.3/ubuntu/noble/amdgpu-install_6.3.60303-1_all.deb
sudo apt install ./amdgpu-install_6.3.60303-1_all.deb
sudo apt update
sudo apt install amdgpu-dkms rocm
```

I'm assuming you have installed Ollama and it's running on your system (linux). Verify by running:

```sh
sudo systemctl status ollama.service
```

It should say it's running.

Stop it.

```sh
sudo systemctl stop ollama.service
```

Edit the configuration file for the service:

```sh
sudo systemctl edit ollama.service
```

This will open a text editor. Add this line to the appropriate place.
It says:
```sh
### Anything between here and the comment below will become the contents of the drop-in file
[Service]
Environment="HSA_OVERRIDE_GFX_VERSION=11.0.2"
### Edits below this comment will be discarded
```

I tried a lot of things online, and most of them said HSA_OVERRIDE_GFX_VERSION should be 11.0.0 - but it was crashing ollama. Version 10.3.0, which was also recommended, was hanging the process - doing nothing. This one worked well. 
