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
    
    Following the steps in [Configure User Limits in Linux Controllers](https://docs.appdynamics.com/display/PRO45/Prepare+Linux+for+the+Controller#PrepareLinuxfortheController-configure_in_linuxConfigureUserLimitsinLinux):
  	<pre><code>
	vi /etc/security/limits.d  	
 	</code></pre>
	And add the following at the end of the file:
	<pre><code>
	* hard nofile 65535
	* soft nofile 65535
	* hard nproc 8192
	* soft nproc 8192 	
 	</code></pre>
	Then logout from the terminal and login again.

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

In the Enterprise Console browser interface.  You will use the Enterprise console to install the Controller and Events Service components of the AppDynamics platform.  
The Controller provides the main AppDynamics GUI which is backed by a MySQL database, while the Events Service is the facility that stores unstructured data generated by various AppDynamics functions.

Installation of both components can be accomplished using the CLI, but that procedure is beyond the scope of this lab.  
Also note that the lab focuses on installation and configuration procedures in a learning environment, but you should be aware that additional steps may be necessary to address security, performance, availability, and scalability considerations in a production deployment.  
Reference documentation can be found on the AppDynamics web site - [Controller Documentation](https://docs.appdynamics.com/display/PRO45/Controller+Deployment) and [Events Service Documentation](https://docs.appdynamics.com/display/PRO45/Events+Service+Deployment).

1. From the Enterprise Console UI, select the Install tab and click the Express Install type.
<img src="https://github.com/sherifadel90/AppDynamicsPlatformInstallation/blob/master/assets/images/03-ExpressInstall-Step.png" width="600">

2. In the **Name the Platform** section, Provide a name of your choice for the platform and confirm the default **Installation Path** value is a subdirectory of the installation directory we specified earlier.
<img src="https://github.com/sherifadel90/AppDynamicsPlatformInstallation/blob/master/assets/images/04-NamePlatform-section.png" width="600">

3. In the **Add a Host** section, choose Use Enterprise Console Host.
<img src="https://github.com/sherifadel90/AppDynamicsPlatformInstallation/blob/master/assets/images/05-AddHost-section.png" width="600">

4. In the **Install the Controller** section:
	- Select the **Demo profile**
	- Set the Controller Admin Username to **admin**
	- Set the Controller **Admin Password** to **welcome1**
	- Set the Controller Controller Root User Password to **welcome1**
	- Set Database Root Password to **welcome1**
	<img src="https://github.com/sherifadel90/AppDynamicsPlatformInstallation/blob/master/assets/images/06-InstallController-section.png" width="600">

5. Click the **Submit** button

The installation will begin immediately but it may take a few minutes before the jobs appear and begin reporting their progress on the page. 

2.5 -	After the jobs have completed successfully (up to 15 minutes), click the Controller and Events Service sections to make sure each shows a health state of Normal.

2.6 -	To confirm the Controller is running properly, verify you can connect to its URL in a web browser and authenticate using the information specified in steps 2.4.b and 2.4.c.

http://<ec2-Public-DNS-name>:8090

2.7 -	Ping the Events Service API port in a browser.  A successful “pong” response confirms the service is running properly.

http://<ec2-Public-DNS-name>:9080/_ping

 
2.8 -	Deploy the provided license file to the controller directory

sudo mv /home/appd/license.lic /opt/appd/platform/product/controller/

2.9 -	In the Controller UI, confirm the new license has been applied by navigating to the Settings icon ( ⚙) in the upper right corner of the page and selecting License.  Make sure the the license details now show it as a Pro Trial edition and includes entitlements for five of each language agent.

2.10 -	Note: If the licenses are not reflected as below, perform a Controller Restart (Without database restart) from the Platform Admin.



