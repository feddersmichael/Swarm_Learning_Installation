
# Swarm Learning 2.1.0 Installation (Version from 15.11.23)

## Overview

Generally speaking the installation of Swarm learning has three steps:

 1. Install the AutoPass License Server (APLS)

 2. Gain your personal Lock Code and use this to download the licenses and the HPE Swarm Learning Management UI (SLM-UI) from HPE Software Center

 3. Install swarm learning on the host with the SLM-UI

## Preliminaries

 Before we start the actual installation of the software we will shortly discuss the Prerequisites. The exact prerequisites for Swarm-Learning can be found on the [github page](https://github.com/HewlettPackard/swarm-learning/blob/master/docs/Install/Prerequisites.md#prerequisites) or the [HPE page](https://support.hpe.com/hpesc/public/docDisplay?docId=sd00001419en_us&page=GUID-04214413-16D0-4870-B55D-F22E7FB61B8F.html#ariaid-title1). The most relevant point to begin with are the supported Operating Systems. On the official documents it's stated that we need either

- Ubuntu version 22.04

- Red Hat Enterprise Linux (RHEL) version 8.5

- SUSE Linux Enterprise Server (SLES) version 15

The only Operating System which is also listed in the [Autopass_Support_Matrix](pdf/Autopass_Support_Matrix.pdf) (which is also included in the download-files for APLS from the HPE Software Center to which we will come [later](#installing-apls-as-a-software)) is RHEL 8.5.

>There are other versions of AutoPass which can be used also under different Operating Systems like Windows 11. **This versions are not compatible with Swarm Learning!** The Lock Code we get there can be used to download Swarm Learning, however when we want to install the software we can not proceed because the Lock Code **needs to come from the HPE version of APLS**.

We can receive RHEL 8 for free through the Red Hat Developer Program (More information about the requirements are [here](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux/developer-program)). To access the program it is necessary to create an account on the Red Hat developers [page](https://developers.redhat.com/) and activate it through a link sent to the selected mail. Afterwards we can choose our version of choice [here](https://developers.redhat.com/products/rhel/download#rhel-new-product-download-list-61451) and download the iso file. The newest version of RHEL 8 available at the creation of this document is RHEL 8.9 and is the version used in this guide.

>In my installation through a boot on a USB flash drive the media was always shown as not suitable for an instalation if I selected the option to check the installation files before installing the iso. However if I selected the direct installation, omitting the check, there didn't occur any problems. This might be highly hardware dependent but could be worth a try if you run into similar problems.

From now on we will talk about the installation process of Swarm Learning assuming its performed on a fully installed version of RHEL 8. For help with the installation of the Operating System consult for example the [official installation guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/performing_a_standard_rhel_8_installation/index).

## Installing APLS

We can follow the instructions [here](https://github.com/HewlettPackard/swarm-learning/blob/master/docs/Install/Install_the_License_Server.md). There are two ways to install APLS:
- As a container through docker
- As a software directly on the host through the HPE Software Center

For both versions we need a HPE Account, the registration can be found [here](https://auth.hpe.com/hpe/cf/registration). We now go through both ways to set up APLS as a [container](#installing-apls-as-a-container) or as a [software](#installing-apls-as-a-software). After installing APLS we can continue for both methods with the [same step to receive the Lock Code](#gain-the-lock-code-and-download-slm-ui).
### Installing APLS as a container

To run the APLS Container we first need to install a docker environment. In principle there are two option:
- [Docker Engine](https://docs.docker.com/engine/)  
or
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)

[comment]: # (rpm package from https://docs.docker.com/desktop/install/fedora/ sudo dnf install gtk3-devel  https://docs.fedoraproject.org/en-US/epel/ https://www.redhat.com/en/blog/whats-epel-and-how-do-i-use-it)

However, with this build on RHEL 8 we run into the problem that Docker Desktop depends on the 'libc.so.6(GLIBC_2.34)(64bit)' package which is only available under RHEL 9. For more background see [here](https://access.redhat.com/solutions/38634) (official) or [here](https://stackoverflow.com/q/40798938) (unofficial). We will therefore stick to Docker Engine.

#### Installing Docker Engine

We first need to install Docker Engine. We can follow the instructions for CentOS [here](https://docs.docker.com/engine/install/centos/#install-using-the-repository).

> The installation suggested for RHEL is only available for the s390x architecture (IBM Z) and **not** relevant for us.

First we need to install a dependency:

    $ sudo yum install -y yum-utils

Then we can install Docker Engine.

    $ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

>In my installation this step only ran through after adding --allowerasing to the code, so in total using  
 `$ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo --allowerasing`

After this command docker engine should be installed and ready to use. We can start it with the following command:

    $ sudo systemctl start docker


Optionally we can test that our installation was successfull by running the 'hello-world' container:

    $ docker run hello-world

> Be aware that we need either root access or have to use *sudo* before every *docker* command.

#### Pulling and starting APLS container

The rest of the steps we follow the [official document](pdf/HPEAutoPassLicenseServerDockerIntegration.pdf) about the Docker integration. This is very similar to the documentation on github, however it includes some extra information and check-ups. 
1. Login to the HPE docker registry using your HPE Passport email id and password ‘hpe_eval’ :

        $ docker login hub.myenterpriselicense.hpe.com -u <HPE-PASSPORT-EMAIL-ID> -p hpe_eval

> 'HPE-PASSPORT-EMAIL-ID' refers to the mail used to register the HPE account [here](https://myenterpriselicense.hpe.com/cwp-ui/software) - to run the code '< >' needs to be removed as well.

2. Enable docker content trust:

        $ export DOCKER_CONTENT_TRUST=1

3. Pull the image with a tag:

        $ docker pull hub.myenterpriselicense.hpe.com/hpe_eval/autopass/apls: <image-tag>

> Possible image-tags are either 9.14 or 9.15

4. Create a volume to retain configurations and installed licenses across containers

        $ docker volume create apls-volume

5. Start the APLS container

        $ docker run -d \
          --name apls \
          -v apls-volume:/hpe \
          -p 5814:5814 \
          --restart unless-stopped \
          hub.myenterpriselicense.hpe.com/hpe_eval/autopass/apls:<image-tag>

> '< image-tag>' Here we need to use the same tag we selected when we pulled the image

The add-ons have the following reason:
- `--name apls` specifies the name under which the container is saved
- `-v apls-volume:/hpe` we bind mount the container to the volume we just created
- `-p 5814:5814` with this command we publish the port 5814 of the containered APLS to the port 5814 in our localhost and therefore enable that we can access the APLS Web Portal
- `--restart unless-stopped` this command makes sure that the container always runs while docker is active unless it gets specifically stopped

After these commands the APLS container should be running. We can check this specifically with the command

    $ docker logs apls

If we don't see warnings and have 'org.apache.catalina.startup.Catalina.start Server startup in [5158] milliseconds' as one of our messages the server should be running.

### Installing APLS as a software

If we want to install APLS as a software directly on the host we can follow these steps:

1. We need to log in to the [My HPE Software Center](https://myenterpriselicense.hpe.com/cwp-ui/free-software/APLS) and get directed to the APLS software page.












TODO: change picture size















2. We can access the software under *get-license*. ![APLS_search](images/Screenshot%20from%202023-11-20%2017-06-34.png)

3. We have to agree to the license and can select all files to donload. ![APLS-download](images/Screenshot%20from%202023-11-20%2017-06-12.png)

2. After downloading the zip files the installation steps are described in the document [HPE AutoPass License Server User Guide.pdf](pdf/HPE%20AutoPass%20License%20Server%20User%20Guide.pdf) under the section *Installation and Setup*. This involves mainly executing the UNIX/setup.bin file in a standard installation procedure.

We start by opening the terminal and moving into the folder where the setup.bin file is saved. Then we can start the installation with the command

    $ sudo ./setup.bin

> If we want to change the default installation folder we can do this with the installer.properties file which has to be in the same folder as setup.bin .

## Gain the Lock Code and download SLM-UI

After having started the docker container we can access the web site through  
https://localhost:5814/autopass

>This is in contrast to the advice in the githup repo where it says to use https://localhost:5814

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