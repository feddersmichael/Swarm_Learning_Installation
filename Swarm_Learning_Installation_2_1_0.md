
# Swarm Learning 2.1.0 Installation (Version from 15.11.23)

## Overview

Generally speaking the installation of Swarm learning has three steps:

 1. Install the AutoPass License Server (APLS) from the HPE Software Center

 2. Gain your personal Lock Code and use this to download the licenses and the HPE Swarm Learning Management UI (SLM-UI) from HPE Software Center

 3. Install swarm learning on the host with the SLM-UI

## Preliminaries

 Before we start the actual installation of the software we will shortly discuss the Prerequisites. The exact prerequisites for Swarm-Learning can be found on the [github page](https://github.com/HewlettPackard/swarm-learning/blob/master/docs/Install/Prerequisites.md#prerequisites) or the [HPE page](https://support.hpe.com/hpesc/public/docDisplay?docId=sd00001419en_us&page=GUID-04214413-16D0-4870-B55D-F22E7FB61B8F.html#ariaid-title1). The most relevant point to begin with are the supported Operating Systems. On the official documents it's stated that we need either

- Ubuntu version 22.04

- Red Hat Enterprise Linux (RHEL) version 8.5

- SUSE Linux Enterprise Server (SLES) version 15

The only Operating System which is also listed in the *Autopass_Support_Matrix.pdf* (which is included in the download-files for APLS from the HPE Software Center to which we will come [later](#installing-apls)) is RHEL 8.5.

>There are other versions of AutoPass which can be used also under different Operating Systems like Windows 11. **This versions are not compatible with Swarm Learning!** The Lock Code we get there can be used to download Swarm Learning, however when we want to install the software we can not proceed because the Lock Code **needs to come from the HPE version of APLS**.

We can receive RHEL 8 for free through the Red Hat Developer Program (More information about the requirements are [here](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux/developer-program)). To access the program it is necessary to create an account on the Red Hat developers [page](https://developers.redhat.com/) and activate it through a link sent to the selected mail. Afterwards we can choose our version of choice [here](https://developers.redhat.com/products/rhel/download#rhel-new-product-download-list-61451) and download the iso file.

>In my installation through a boot on a USB flash drive the media was always shown as not suitable for an instalation if I selected the option to check the installation files before installing the iso. However if I selected the direct installation, omitting the check, there didn't occur any problems. This might be highly hardware dependent but could be worth a try if you run into similar problems.

From now on we will talk about the installation process of Swarm Learning assuming its performed on a fully installed version of RHEL 8. For help with the installation of the Operating System consult for example the [official installation guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/performing_a_standard_rhel_8_installation/index).

## Installing APLS

In principle we can follow the instructions [here](https://github.com/HewlettPackard/swarm-learning/blob/master/docs/Install/Install_the_License_Server.md). There are two ways to install APLS:
- As a container through docker
- As a software directly on the host through the HPE Software Center
We will go through both of them separately.

### Installing APLS as a container

#### Docker Engine
docker engine mit
https://docs.docker.com/engine/install/centos/ with --allowerasing  

We can then follow all the "docker" commands as in the instruction. Be aware that we need either root access or have to use *sudo* before every docker command.

After having started the docker container we can access the web site through  
https://localhost:5814/autopass

#### Docker Desktop
rpm package from 
https://docs.docker.com/desktop/install/fedora/  

gtk3-devel is needed by docker-desktop-4.25.1-128006.x86_64  
sudo dnf install gtk3-devel  
libc.so.6(GLIBC_2.32)(64bit) is needed by docker-desktop-4.25.1-128006.x86_64  
libc.so.6(GLIBC_2.34)(64bit) is needed by docker-desktop-4.25.1-128006.x86_64  
Might be connected with general support.  
official https://access.redhat.com/solutions/38634  
and inofficial https://stackoverflow.com/q/40798938  
pass is needed by docker-desktop-4.25.1-128006.x86_64  
pass we can download from the "epel" repository. It is not installed by default but has to be activated.  
The instructions are here https://docs.fedoraproject.org/en-US/epel/  
Some general information is here https://www.redhat.com/en/blog/whats-epel-and-how-do-i-use-it  
keep in mind that the installation there is for RHEL 9
qemu-system-x86 >= 5.2.0 is needed by docker-desktop-4.25.1-128006.x86_64

### Installing APLS as a software


In the latest version the main installation way was changed to downloading the APLS container. If you haven't purchased any version of Swarm Learning the way to follow is a bit different.
1. We need to make a HPE account [here](https://auth.hpe.com/hpe/cf/registration)
2. Then we can find the APLS software under this [link](https://myenterpriselicense.hpe.com/cwp-ui/free-software/APLS). 
3. After downloading the zip files the installation steps are described in the document *HPE AutoPass License Server User Guide.pdf* under the section *Installation and Setup*. This involves mainly executing the setup.bin file in a standard installation procedure.

## Gain the Lock Code and download SLM-UI

To access the Lock Code we have to access the AutoPass Web Server. The steps are again listed in the *HPE AutoPass License Server User Guide.pdf* under the section *HPE AutoPass License Server Setup and Start*. I will receite them here:

1. After installing APLS succesfully we can open it by accessing it over the port 5814  Specifically we need to enter he server's Web address into our browser which is:  
https://< ip address or Host Name >:5814/autopass  
Hereby we need to replace ip-adress or Host Name with the respective name on the users account and also remove the < >
    
2. We can then log in by using
    - **User name**: admin
    - **password**: password  
    The system will then ask to create a new user password combination which can be chosen freely.

3. Under the tab *License Management* and the subtab *Install License* we find the **Lock Code**.

We can now continue with downlaoding the SLM-UI

1. We can access the [HPE Software page](https://myenterpriselicense.hpe.com/cwp-ui/software) and search for *Swarm Learning*. Click **Software** (left panel) -> Under **Search** Select "Product Info" -> enter the string "Swarm Learning". Under the search results, For the product "HPE-SWARM-CMT 2.0.0"-> Click on **Action** -> **Get License**

2. We can activate our license with the Lock Code and then download all available files (the installation files for Windows and MacOS can be omitted).

## Install swarm learning on the host with the SLM-UI

- Now we need to install the licenses. We can just follow the steps as we did to look up the Lock Code. The file we need to uplaod has the ending .dat . We then just follow the shown steps.

Afterwards we can install the SLM-UI.

1. The first thing to fill out is at step 3. *Docker Registry*. Hereby the **HPE Passport Username** is the User ID / Email Adress which we use to login at the HPE Software portal.

2. At step 5. *Install SLM-UI* we need to specify our IP adress which we get from the terminal by using the comman *ip a* and choosing the currently used internet connection. We also need to check the ssh port, this is possible for example with *sudo netstat -lntp*. Here we find the port under the PID *1239/sshd* and then check the local adress in the same row. Finally we have to fill out *Username* and *Password*. With this the program refers to the login data of the current logged in user of the RHEL OS.

>Especially the current user needs a password. If no password is set the installation process can not be performed.

After this the installation process should run through and install Swarm Learning into the folder specified in step 5.