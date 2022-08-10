---
layout: post
title: VMs using Multipass and WSL2
---
Virtual machines are always the talk of the town. Isn't running a machine within a machine amazing? With the current technology, you can make your current machine to host a guest. The guest shares the host's hardware and has its own kernal and operating system. There are number of ways to use virtual machines on your host machine. In this post, we will compare two such ways to use VMs - Multipass and WSL2 

## Build VMs
### With multipass

Multipass is a lightweight, commandline VM manager developed by Canonical team, for quickly deploying Ubuntu instances on our local machine. It uses KVM on Linux, Hyper-V on Windows and HyperKit on macOS to run the virtual machine with minimal overhead. It can also use VirtualBox on Windows and macOS. In this article, we will use VirtualBox to run Multipass. We will need Oracle VM VirtualBox on our host machine to run Multipass. After [installation](https://multipass.run/), check the multipass version from Windows Powershell

The below line will launch a VM with 20G disk storage and 2G memory and `--network` command will bridge the VM with host network. 

`multipass launch --network WiFi -d 20G -m 2G` 

### With WSL2

The Windows Subsystem for Linux (WSL) allows us to run command-line tools and some linux applications directly on the Windows. After [installation](https://docs.microsoft.com/en-us/windows/wsl/install), install the required distribution system and we are cool! You can check the memory allocated for your VM and ways to limit it from [here](https://www.aleksandrhovhannisyan.com/blog/limiting-memory-usage-in-wsl-2/).


## Yet to be completed...
{% comment %} 
## Password protection

### Multipass

### WSL2



## Connect to Visual Studio Code

### Multipass

### WSL2


https://docs.docker.com/desktop/windows/wsl/
https://blog.jayway.com/2017/04/19/running-docker-on-bash-on-windows/ 

{% endcomment %}
