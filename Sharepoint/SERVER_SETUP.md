# Sharepoint Server Setup Guide

Deploy guide for Sharepoint Single Farm Server 2019 in Windows Server 2019

Requirements: Windows Server 2019, Sharepoint Server 2019, SQL Server 2017

## Deploy steps:

1, Deploy your Windows Server 2019 , make sure your VM meets the requirements [here](https://docs.microsoft.com/en-us/sharepoint/install/hardware-and-software-requirements-2019)

I'm using a EC2 instance with a Windows Server 2019 AMI. It has 16 GB RAM, 4 CPUs and 250 GB disk space.  
You can choose whatever VM you like as long as it meets the hardware requirements in above link.

2, Once the Windows server is ready, download Sharepoint Server 2019 [here](https://www.microsoft.com/en-us/download/details.aspx?id=57462) 

Install software prerequisites first, then launch Sharepoint Server 2019 Setup, follow the instructions and finish the installation.

3, Make sure you have SQL Server 2017 (or other versions) installed on your Windows Server. You can download SSMS for controlling and monitoring your SQL server.

4, Install Active Directory Domain Controller on your Windows Server, and then promote the server to the domain controller. 

## References:

* https://www.youtube.com/watch?v=yWVMhg1x__w&t=322s&ab_channel=DesertEngineer
* https://sharepoint.handsontek.net/2021/08/03/install-sharepoint-subscription-edition-step-step-hyper-v-part-1-5/

