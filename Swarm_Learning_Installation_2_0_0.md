
# Swarm Learning 2.0.0 Installation (Version from 09.11.23)

## Overview

Generally speaking the installation of Swarm learning has three steps:

 1. Install the AutoPass License Server (APLS) from the HPE Software Center

 2. Gain your personal Lock Code and use this to download the licenses and the HPE Swarm Learning Management UI (SLM-UI) from HPE Software Center

 3. Install swarm learning on the host with the SLM-UI

## Preliminaries

 Before we start the actual installation of the software we should have a short look into the requirements.  
 The current version of APLS available at HPE Software Center is 9.15. Noticably the supported Operating systems are

- Windows 10

- Windows Server 2012 R2

- Windows Server 2016

- Red Hat Enterprise Linux 7 and 8

- CentOS 7 and 8

The only options not requiring a purchased license are CentOS and Red Hat Enterprise Linus (RHEL). However CentOS is disontinued at the time of the creation of this document. With RHEL 7 and 8 we can only receive RHEL 8 for free through the [Red Hat Developer Program](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux/developer-program). After making an acoount we can download the Iso of RHEL 8.

From now on we will talk about the installation process assuming its performed on RHEL 8.

>There are other versions of AutoPass which can be used also under different Operating Systems like Windows 11. **This versions are not compatible with Swarm Learning!** The Lock Code we get there can be used to download Swarm Learning, however when we want to install the software we can not proceed because the Lock Code **needs to come from the HPE version of APLS**.

## Installing APLS

In principle we can follow the instructions [here](https://github.com/HewlettPackard/swarm-learning/blob/master/docs/Install/Install_the_License_Server.md). In the latest version the main installation way was changed to downloading the APLS container. If you haven't purchased any version of Swarm Learning the way to follow is a bit different.
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