# AppDynamics Platform Installation (LINUX)

## Description

This lab guides you through the process of installing and configuring the AppDynamics platform components for an on-premise deployment.  The architecture diagram below shows these components with their communication paths and payloads.  
<img src="https://github.com/sherifadel90/AppDynamicsPlatformInstallation/blob/master/assets/images/00-onpremise-diagram.jpg" width="600">

In this Lab for simplicity we will install all AppDynamics Components (Enterprise Console, Controller, Events Service, End User Monitoring Server) on a Standalone Virtual Machine.

In this Lab, you will learn to:
1. Install the Enterprise Console (aka Platform Admin) tool
2. Install and configure the AppDynamics Controller software
3. Install and configure the Events Service
4. Install and configure the End User Monitoring (EUM) Server

The software versions used in the lab are the most recent generally available at the time the lab was created.  As subsequent versions continue to be released, expect slight variations to command line output and screen shot graphics.  Though normally inconsequential, it is recommended to note these differences for reference in troubleshooting should they become significant.

## Setup

### Step 1: Prepare Virtual Machine Specs.

- **Operating System:** Linux or Windows  (The Guide below is based on Linux OS)
- **Linux Distribution:** Any from the below list (The Guide below is based on CentOS)
![SupportedOperatingSystems](assets/images/00-Supported-OperatingSystems.png)
- **CPU:** 2 Cores (Minimum)
- **Memory:** 8 GB RAM (Minimum)
- **Storage:** 60 GB (Mimimum)

### Step 2: Enterprise Console Requirements

The Enterprise Console can run on the same host as the Controller and the embedded Events Service. If this is the case, the machine you choose to run the Enterprise Console must meet the requirements for all the components that run on that machine.

Reference documentation can be found on the AppDynamics documents site - [Enterprise Console Requirements](https://docs.appdynamics.com/display/PRO45/Enterprise+Console+Requirements, "Enterprise Console Requirements").

1. We will need to install these required libraries
	<pre><code>
 	yum install libaio
	yum install numactl
	yum install tzdata
	yum install ncurses-libs
 	</code></pre>
	Note: the above required libraries are based on Red Hat and CentOS, for other Distros, please refer to [Enterprise Console Requirements](https://docs.appdynamics.com/display/PRO45/Enterprise+Console+Requirements, "Enterprise Console Requirements").

2.  AppDynamics requires the following hard and soft per-user limits in Linux: 
    * Open file descriptor limit (nofile): 65535
    * Process limit (nproc): 8192  

https://docs.appdynamics.com/display/PRO45/Prepare+Linux+for+the+Controller#PrepareLinuxfortheController-configure_in_linuxConfigureUserLimitsinLinux

### Step 3: Install Enterprise Console (aka Platform Admin)

In this exercise, you will be setting up the Enterprise Console.  This utility provides a browser-based user interface that allows an AppDynamics administrator to install and manage the Controller and Events Service components of the AppDynamics platform.  A CLI for the Enterprise Console is available, but is outside the scope of this lab.  Reference documentation can be found on the AppDynamics documents site - [Enterprise Console Documentation](https://docs.appdynamics.com/display/PRO45/Enterprise+Console, "Enterprise Console Documentation").

1. Download the Platform Admin software to the lab host.
   Log into that site with permissions to download the "Enterprise Console - 64-bit Linux(sh)"
![EnterpirseConsoleDownload](assets/images/01-EnterpriseConsoleDownload.png)

2. Copy the .sh file to your Host either using SCP on if your Desktop is MAC/Linux or using WINSCP if your Desktop is Windows

3. On the Host, Make the installer script executable:
	<pre><code>
 	chmod a+x platform-setup-x64-linux-20.x.x.x.sh
 	</code></pre>

4. Install the Platform Admin software:
	<pre><code>
	sudo su
 	./platform-setup-x64-linux-20.x.x.x.sh
 	</code></pre>
    with inputing the below
    	<pre><code>
	I accept the agreement: 1
	Where should AppDynamics Enterprise Console be installed?: /opt/appdynamics/platform
	Database Root User Password: AppD123
	Database Port: 3377 (default)
	Enterprise Console Database Password: AppD123
	Enable Https Connection: n
	Enterprise Console Host Name: In case of AWS, Enter the public DNS name of the lab EC2 instance
	Enterprise Console Port: 9191 (default)
	Enterprise Console Root User Name: admin (default)
	Enterprise Console Root User Password: AppD123
 	</code></pre>
    After a few minutes, you should see output similar to that shown below...
    	<pre><code>
    	Setup has finished installing AppDynamics Enterprise Console on your computer.
	To install and manage your AppDynamics Platform, use the Enterprise Console
	CLI from /opt/appdynamics/platform/platform-admin/bin directory.
	Finishing installation ...
    	</code></pre>

5. To confirm the Enterprise Console is functioning properly, verify you can connect to its URL in a web browser and authenticate using the information specified in the installation (Username: admin, Password: AppD123)
 	<pre><code>
	http://[your-ip-address]:[EnterpriseConsolePort]
	</code></pre>
	<img src="https://github.com/sherifadel90/AppDynamicsPlatformInstallation/blob/master/assets/images/02-EnterpriseConsoleLogin.png" width="800">

### Step 4: AppDynamics Controller & Events Service

This exercise begins where the last one ended - in the Enterprise Console browser interface.  You will use the Enterprise console to install the Controller and Events Service components of the AppDynamics platform.  The Controller provides the main AppDynamics GUI which is backed by a MySQL database, while the Events Service is the facility that stores unstructured data generated by various AppDynamics functions.

Installation of both components can be accomplished using the CLI, but that procedure is beyond the scope of this lab.  Also note that the lab focuses on installation and configuration procedures in a learning environment, but you should be aware that additional steps may be necessary to address security, performance, availability, and scalability considerations in a production deployment.  Reference documentation can be found on the AppDynamics web site - Controller Documentation and Events Service Documentation.


