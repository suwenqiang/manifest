# ASUS Yocto BSP build SOP
1.	Create an Ubuntu machine environment (Using one of the following versions is recommended: 16.04/18.04/20.04.)
2.	Download Yocto source code from github
```
  $ git config --global user.name "Your Name"
  $ git config --global user.email "Your Email"

  $ mkdir ~/bin (this step may not be needed if the bin folder already exists)
  $ curl https://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
  $ chmod a+x ~/bin/repo
  $ export PATH=~/bin:$PATH

  $ mkdir ~/asus-yocto-sumo
  $ cd ~/asus-yocto-sumo

  $ repo init -u https://github.com/ASUS-IPC/manifest.git -b yocto-sumo-imx_8m -m asus-pe100a-4.14.98-1.0.15.xml
  $ repo sync -j4
```
3.	Setup Docker
> Uninstall old versions
```
  $ sudo apt-get remove docker docker-engine docker.io containerd runc
```
Install Docker Engine - Community
```
  $ sudo apt-get update
  $ sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
  $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
  $ sudo apt-key fingerprint 0EBFCD88
    $ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
  $ sudo apt-get update
  $ sudo apt-get install docker-ce docker-ce-cli containerd.io
  $ sudo docker run hello-world
```
> https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/

Manage Docker as a non-root user
> If you don’t want to preface the docker command with sudo, create a Unix group called docker and add users to it.
```
  $ sudo groupadd docker
  $ sudo usermod -aG docker $USER
  $ sudo reboot
  
  $ docker run hello-world
```
> https://docs.docker.com/install/linux/linux-postinstall/

4.	Docker build:
> Go to to the directory where the downloaded the code base is located in, and execute the script. This will take a while as it installs the necessary packages on the host and builds the Docker image.
```
  $ cd ~/asus-yocto-sumo
  $ ./docker_builder/docker-builder-run.sh
```
> Once the step above is finished, you are in the shell of the newly started Docker container. You can issue the following command to build all the images for yocto.
Below was sample command for ASUS PE100A, If you want to build image for other HW device, please change the MACHINE name (ex. imx8mq-pv100a for ASUS PV100A)
```
  $ DISTRO=fsl-imx-xwayland MACHINE=imx8mq-pe100a EULA=1 source fsl-setup-release.sh -b build_imx8mq-pe100a
  $ bitbake fsl-image-qt5-validation-imx
```
After build successfully, you can find all image at below path
> Bootloader Image：
```
  $ build_imx8mq-pe100a/tmp/deploy/images/imx8mq-pe100a/imx-boot-imx8mq-pe100a-sd.bin
```
> Full Raw Image：
```
  $ build_imx8mq-pe100a/tmp/deploy/images/imx8mq-pe100a/fsl-image-qt5-validation-imx-imx8mq-pe00a.sdcard.bz2
```
