<h1 align="center">Azure SEIM, Honeypot & Live Attacks</h1>


### **Microsoft Sentinel SIEM with Honeypot Integration on Windows**

In this project, I set up Azure Sentinel as a SIEM system in a Windows environment. I connected it to a live Windows VM acting as a honeypot to log and analyze real-time RDP brute force attacks. I also used a custom PowerShell script to retrieve geolocation data of attackers and visualized live attack patterns on an Azure Sentinel map.
#

### Key Features
- Configured Azure Sentinel as a SIEM system.
- Connected a Windows VM as a honeypot to log live attacks.
- Used PowerShell scripts to extract attacker geolocation.
- Visualized attack data on the Azure Sentinel map.

#
### Results
- Successfully identified and monitored real-time RDP brute force attacks.
- Extracted geolocation data for attacker IPs and visualized it on an Azure Sentinel map.
- Demonstrated the capabilities of Azure Sentinel in analyzing and visualizing security threats.

This project demonstrated my ability to deploy and configure a SIEM solution, integrate log analytics, and use automation to enrich data with external geolocation information.
#
#### These picture show custom logs being output with geodata & the world map of incoming attacks 24+ hours after building custom logs/geodata 
![unnamed-3](https://github.com/user-attachments/assets/d747dcda-71bd-4110-a3d3-1145222f59f3)


<img width="749" alt="Screenshot 2025-01-18 at 4 44 39 PM" src="https://github.com/user-attachments/assets/e7f47474-7b24-4dc0-aa34-3edc6f3ae8ca" /> 

# 
⬇️ Below are the steps I took, with pictures included ⬇️
##

#### **Creating the Virtual Machine (VM)**

1.	I created a new Virtual Machine (VM) in Azure and assigned it to a resource group named honeypotlab.

2.	I configured the VM with the following instance details:
  - Name: honeypot-vm
  -	Region: West US 2
  - Availability options: No infrastructure redundancy required
  -	Security type: Standard
  -	Image: Windows 11 Enterprise, version 22H2 (x64 Gen2)
  -	VM Architecture: x64
  -	Size: Standard_D2s_v3 (2 vCPUs)

3.	For the administrator account, I used password authentication.

4.	I set inbound port rules to allow RDP (3389) traffic.

5.	I enabled advanced network security by creating a new NIC network security group. In this group:
  -	I removed the default rule.
  -	I added a new inbound rule with a wildcard (*) as the destination port, set the priority to 100, and named it Danger.

6.	Finally, I clicked Review + Create to provision the VM.
Note: The VM was configured to be highly discoverable, which is not recommended for production environments but was necessary to entice attackers for this lab.
<img width="699" alt="Screenshot 2025-01-16 at 1 52 23 PM" src="https://github.com/user-attachments/assets/d8022f27-5afb-483a-89c0-0bd2a4c9e5b3" />


<img width="570" alt="Screenshot 2025-01-16 at 1 38 54 PM" src="https://github.com/user-attachments/assets/9eae731c-6060-424e-a584-0d0beb830d3d" />


#
#### Creating a Log Analytics Workspace
Next, I created a Log Analytics workspace to store logs from the VM and connect them to Microsoft Sentinel:

1.	I navigated to Log Analytics Workspaces and clicked Create.

2.	I selected the same resource group (honeypotlab) and named the workspace honeypotlog.

3.	I kept the region as West US 2 and finalized the setup by clicking Review + Create.

#
#### Enabling Log Collection with Microsoft Defender for Cloud
To gather logs from the VM, I enabled Microsoft Defender for Cloud:

1.	I opened Microsoft Defender for Cloud and navigated to Environment Settings under Management.

2.	I expanded the Tenant Root Group, selected the Azure subscription, and turned on Defender CSPM.

3.	I saved the settings and enabled monitoring.

#
#### Connecting the VM to the Log Analytics Workspace
1.	I returned to the Log Analytics workspace and selected honeypotlog.

2.	Under Virtual Machines, I selected honeypot-vm and clicked Connect to link it to the workspace.

#
#### Setting Up Microsoft Sentinel
1.	I opened Microsoft Sentinel and created a new Sentinel instance, connecting it to the honeypotlog workspace.

2.	I added the workspace to Sentinel and began using it to visualize attack data.

#

#### Accessing the VM Using RDP
1.	I navigated back to the Azure portal and copied the public IP address of honeypot-vm.

2.	Using Remote Desktop on my Windows computer, I logged into the VM with the same Azure credentials.

3.	Once connected, I began monitoring and analyzing logs.

![unnamed-1](https://github.com/user-attachments/assets/5238fb76-e72f-469f-bfe2-bd46566300ee)

# 

#### Analyzing Logs Inside the Windows VM
1.	Inside the VM, I opened Event Viewer and navigated to Windows Logs > Security.

2.	To simulate failed login attempts, I intentionally used incorrect credentials via Remote Desktop and confirmed the failed attempts in Event Viewer.

![unnamed](https://github.com/user-attachments/assets/d7a926d5-4da1-4823-a47b-e8ab712d514f)



![unnamed-2](https://github.com/user-attachments/assets/cfd9d1a9-db03-4168-9bfa-d6bc57128688)


#  

#### Gathering Geolocation Data of Attackers
To discover where attackers were coming from, I:
1.	Turned off the Windows Defender firewall on the VM to make it more discoverable.

2.	Used PowerShell ISE to run a custom script that:
  -	Scanned Event Viewer for failed login attempts.
  - Queried geolocation data for the IP addresses using the ipgeolocation.io API.
  -	Generated a log file (failed_rdp.log) with geolocation information.

![unnamed-3](https://github.com/user-attachments/assets/161707ec-e4b9-463e-a934-90d639126f5c)



![unnamed-4](https://github.com/user-attachments/assets/6d9c37c1-14c5-4d45-8043-bb8f9e160999)


#

#### Creating a Custom Log in the Log Analytics Workspace
1.	I created a custom log in the Log Analytics workspace to store the geolocation data.

2.	I copied the failed_rdp.log file from the VM, uploaded it to the workspace, and defined the log path (C:\ProgramData\failed_rdp.log).

3.	I named the custom log FAILED_RDP_WITH_GEO and saved the configuration.

   <img width="1115" alt="Screenshot 2025-01-18 at 1 49 21 PM" src="https://github.com/user-attachments/assets/bb6dbb56-d45d-46cd-bc60-d06cc33577cc" />


![unnamed-5](https://github.com/user-attachments/assets/c3ea3824-3fd8-4555-aedd-fbe7790f813d)

#

#### Visualizing Attack Data in Microsoft Sentinel
1.	I opened Microsoft Sentinel and created a workbook.

2.	Using the query builder, I wrote a query to extract and display geolocation fields (latitude, longitude, country) from FAILED_RDP_WITH_GEO.

3.	I visualized the attack data on a Map and saved the workbook.

<img width="1278" alt="Screenshot 2025-01-18 at 2 54 40 PM" src="https://github.com/user-attachments/assets/6cba5559-c614-4325-82fb-702551abf65b" />



<img width="1461" alt="Screenshot 2025-01-18 at 2 57 00 PM" src="https://github.com/user-attachments/assets/f53959c1-932a-4fd8-b541-a5d4a3c93ce4" />

#

#### Observing Live Attacks

With the setup complete, I monitored live attack data in Microsoft Sentinel, including failed login attempts and their geographic origins, all visualized on an interactive map.
