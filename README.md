<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDP, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Step 1: Create a Resource Group in Microsoft Azure, 1 Virtual Machine for Microsoft Windows, 1 Virtual Machine for Linux Ubuntu
- Step 2: Connect the Micrsoft Windows VM via Remote Desktop Connection
- Step 3: Download and Install Wireshark through the Windows VM
- Step 4: Observe ICMP in Wireshark & Open the command line in Windows (Powershell) and ping/connect to Linux Ubuntu VM
- Step 5: Test out various other networking protocols (SSH, RDP, DNS, HTTP/S, ICMP)

<h2>Actions and Observations</h2>

<h3>1: Create Resource Group and Virtual Machines: Windows & Linux</h3>

<Strong>What are Resource Groups?</strong>

<p>Resource groups in Microsoft Azure are containers that help manage and organize related Azure resources. They provide a logical grouping for resources -- such as virtual machines, storage accounts, and networks -- allowing administrators to manage, monitor, and apply policies to the resources collectively.</p>

<Strong>What are Virtual Machines?</Strong>

<p>Virtual machine are like computers within computers. They are software emulations of physical computers that allow you to run multiple operating systems or applications on a single physical machine. Virtual machines provide flexibility, allowing you to create and use different environments or systems without the need for separate physical hardware.</p>

<h4>Process of Creating Resource Group</h4>

1. Within Microsoft Azure, navigate to 'Resource Groups' via the search bar or quick access icon. Select 'Create Resource Group'
2. Name the Resource Group: "RG-NSGP" (ResourceGroup--NetworkSecurityGroupsProtocol)
3. It is recommended to place the Resource Group in the United States (Example: (US) West US 3)
4. There is nothing left for us to alter so select: 'Review + Create'

<img src="https://i.imgur.com/7C9XtMr.png" height="80%" width="80%" alt="Creating RG-NSGP Resource Group"/>

<br>

<p>Now that the Resource Group has been created, we can place both of our Virtual Machines (Windows & Linux Ubuntu) inside of it. First, we will create our Windows Virtual Machine.</p>

<h4>Process of Creating Windows VM:</h4>

1. Within Azure, naviagte to 'Virtual Machines' via the search bar or quick access icon. Select 'Create Virtual Machine'
2. Next to ‘Resource Group’, select the Resource Group we just created: "RG-NSGP"
3. VM Name: 'VM1-Windows', Operating System Image: ‘Windows 10 Pro, version 22H2 - x64 Gen2', Size: 2vcpus (anything above 1vcpu will allow the VM to run efficiently. Otherwise, it'll be slow and lag.)
4. Create a username and password. We will be asked for this later when connecting to our VM via Remote Desktop Connection. 
5. Select the blue check mark under Licensing: 'I confirm I have an eligible Windows 10/11 license with multi-tenant hosting rights.’
6. There is nothing to change here, but take notice on how Azure automatically assigned/created a Virtual Network. In this case, it is named ‘(new) VM1-Windows-vnet’. When creating our Linux Virtual Machine, we’ll want to make sure it is also on this same network so that our two Virtual Machines are under one network and can communicate with one another.
7. Select: 'Review + Create'

<img src="https://i.imgur.com/7lD5Ddk.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/pcjMUcX.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/tuUWdN9.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<br>

<h4>Process of Creating Linux VM:</h4>

1. Go to Azure > naviagte to 'Virtual Machines' via the search bar or quick access icon. Select 'Create Virtual Machine'
2. Next to 'Resource Group', we want the Resource Group created previously: "RG-NSGP"
3. VM Name: 'VM2-Linux', Operating System Image: 'Ubuntu Server 20.04 LTS -x64 Gen2', Size: 2vcpu
4. Under ‘Administrator Account’, instead of selecting ‘SSH’, we want to select ‘Password’ and create a username and password again. This username and password will be necessary when we access the Linux command-line from our Windows VM later
5. Forward from ‘Next: Disks >’ and then to ‘Next: Networking >’: 

<p>We can see that we have the same virtual network as our Microsoft Windows VM: 'VM1-Windows-vnet'. Again, this allows for our Virtual Machines to better communicate with one another as they are now on the same network.</P>

6. There is nothing left to alter so we will select ‘Review + Create’ and ‘Create’

<br>

<img src="https://i.imgur.com/tT0HC9v.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/MH0oDyb.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/AnZpMIx.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<br>

<h3>Step 2: Connect to the Microsoft Windows VM via Remote Desktop Connection</h3>

<p>Both our Windows and Linux Virtual Machines have been created. Now, it’s time to connect to our Windows Virtual Machine using Remote Desktop Connection and hop inside. We want to be able to access our Microsoft Operating System so that we can start observing Network Protocols.</p>

<Strong>What are Public IP Addresses?</Strong>

<p>We are going to be copying the Public IP Addresses of our Virtual Machines in order to get inside of them. Public IP addresses are unique numerical identifiers assigned to devices connected to the internet. They allow devices to communicate with other devices and services on the internet. Just like a home address helps identify where you live, a public IP address helps identify and locate devices, such as computers or routers, on the internet, enabling them to send and receive data across the global network.</p>

<h4>Process of Connecting VM:</h4>

1. Within Azure > Select 'Virtual Machines' > Select 'VM1-Windows' > Copy the Public IP Address
2. Those with Microsoft Windows on their physical machine, navigate to the search bar on desktop and type 'Remote Desktop Connection'. Those with MacOS on their physical machine, navigate to the App Store & download 'Microsoft Remote Desktop'

<br>

<img src="https://i.imgur.com/xiKPKRF.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<p>(I'm personally using MacOS so the following images will be taken from that perspective. However, the process for Microsoft Users is the same.)</P.

3. Open 'Microsoft Remote Desktop' > Click 'Add PC'
4. Under PC Name: paste the Public IP address
5. Double Click the recently added VM and sign in using the username/password created in Step 1
6. Press ‘continue’ until you reach the ‘Welcome’ screen for WindowOS

<p>We are now inside of our Windows Virtual Machine. The next step is to download/install Wireshark inside of our Windows Virtual Machine so that we can now finally observe Network Security Groups and Networking Protocols in real time.</p>

<br>

<img src="https://i.imgur.com/5e56TZx.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/CDEUvBI.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>


<br>

<h3>3: Download and Install Wireshark through Windows VM</h3>

<strong>What is Wireshark and why is it useful?</strong>

<p>Wireshark is a protocol analyzer, meaning it captures and analyzes network traffic in real-time. It helps in understanding and diagnosing network-related issues by giving detailed insights/information into the communication happening between devices on the network. Since you can peak into all the traffic coming through, it's useful in identifying problems, troubleshooting network performance, and detecting security vulnerabilities.</p>

<h4>Process for Downloading Wireshark:</h4>

1. Inside of our Microsoft Windows VM, navigate to 'Microsoft Edge > Search 'Download Wireshark'

<p>The link is also here:https://www.wireshark.org/download.html</p>


2. We want to download Wireshark for Windows Installer 64-bit since that is the current operating system we are using
3. Once downloaded, you can access Wireshark via the top right-hand corner by double-clicking ‘Open file’ or clicking ‘File Explorer’ at the bottom of the screen and navigating to ‘Downloads’
4. The App Installer for Wireshark will open. Keep selecting ‘Next’, ‘Noted’, ‘Install’, and ‘Finish’. We will install Wireshark with all of its default settings. There is nothing we need to alter with the application. Agree to all terms.

<br>

<img src="https://i.imgur.com/0kFOrdE.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/a6pPJIp.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<br>


5. Navigate to the search bar on Windows Desktop and type ‘Wireshark’ > Open the application
6. Select ‘Ethernet’ > Click the blue icon shark fin in the top left-hand in order to start capturing packets

<p>We are now inside of Wireshark inside of our Windows Virtual Machine. Wireshark is capturing and displaying data packets. It seems like it’s spamming right now, but it’s essentially showing everything that’s going on with our computer behind the scenes. 

We will now filter and observe various different network protocols and connect to our Linux Virtual Machine.</p>

<img src="https://i.imgur.com/RHRptNX.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/03qR8ty.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>


<br>

<h3>4: Observe ICMP Protocol & Connect to Linux Virtual Machine</h3>

<h4>Part 1: Obeserving ICMP Traffic & Ping Command</h4>

<Strong>What is ICMP?</strong>

<p>Inside of Wireshark, we will filter by ICMP. ICMP (Internet Control Message Protocol) is a network protocol that allows devices on a network to send control and error messages to each other. It is primarily used for diagnostic and troubleshooting purposes. ICMP messages include things like ping requests and error notifications, helping to check if a device is reachable, measure network latency, and identify network issues such as unreachable hosts or congested connections. Essentially, ICMP is like a messaging system that helps devices communicate about network conditions and status.</p>

<h4>Process for Observing ICMP Traffic:</h4>

1. Inside Wireshark, we can filter by ICMP by navigating to the search by at the top section, typing ‘ICMP’, and hitting the ‘Enter’ key
2. The screen should clear as we are now only observing ICMP traffic. We will create some ICMP traffic by pinging/connecting to our Linux Virtual Machine

<br>

<img src="https://i.imgur.com/PqLXwFg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<br>

3. To connect to our Linux VM, open the command-line in Windows (Powershell): Microsoft Desktop > Search bar > type "Powershell"

<br>

<img src="https://i.imgur.com/ZGJtU64.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<br>

<p>We need the Private IP address of our Linux Machine</p>

<Strong>What is the purpose in connecting to a Private IP address?</strong>

<p>Connecting to a private IP address is typically done within a local network or a private network environment. The purpose of connecting to a private IP address is to access and communicate with devices or services within that network. Private IP addresses are not directly accessible from the internet but are used for internal network communication. By connecting to a Private IP address, you can interact with resources such as computers, servers, printers, or other devices within the local network, enabling tasks like file sharing, remote management, or accessing network services within that private network.</p>

4. Go to Azure portal (outside of the Windows VM) > select 'Virtual Machines' > select 'VM2-Linux' (our Linux VM)
5. Scroll down slightly & under the "Networking" subheading, we can see our Private IP address (example: 10.0.0.5) > Copy Private IP address

<br>

<img src="https://i.imgur.com/f5pyQXX.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<br>

6. Navigate back to Microsoft Windows VM command-line (Powershell) > we are going to ping to our Linux VM > Type "ping 10.0.0.5" > we can see data packets being transmitted on Wireshark as our two machines talk to one another via request/reply

<Strong>What does the ping command do?</strong>

<p>The "ping" command is like sending a message to another device on a network to see if it's there and how long it takes for the message to come back. It helps you check if a device is reachable and measure the time it takes for data to travel between your device and the target device. It's like saying "Hey, are you there?" and waiting for a response to know if the device is active and how fast it responds.</p>


<img src="https://i.imgur.com/valueBV.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<br>

7. We're going to connect to an external website (example: www.google.com) > Type "ping www.google.com" in the command-line

<p>Pinging to an external, public website allows us to check connectivity to the internet. If pinging to www.google.com fails, it typically indicates that the computer is unable to establish a network connection or reach the destination server (in this case, google.com). This could be due to various issues such as a loss of internet connectivity, DNS resolution problems, firewall restrictions, or a problem with the target server itself.</p>

8. We can see in Wireshark, the reply/request from another IP address that is responding back to us. We can see it is one of Google’s public IP addresses. This means we have connectivity to the internet

<img src="https://i.imgur.com/GM54FCF.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>


<br>

<h4>Part 2: Network Security Groups in Action</h4>

<p>Now that we have pinged successfully to our Linux VM and have observed connectivity to the internet, we will create a never-ending, perpetual ping to our Linux VM. This is so we can later witness Network Security Groups and Firewalls in action.</p>

<strong>What are Network Security Groups in Azure?</strong>

<p>Network Security Groups (NSGs) in Azure are a feature of Azure Networking that provide network traffic filtering and security for virtual networks and subnets. They act as virtual firewalls, allowing you to define inbound and outbound traffic rules based on source IP addresses, destination IP addresses, ports, and protocols.

NSGs help control access to Azure resources by permitting or denying network traffic to and from virtual machines, subnets, or specific IP addresses. They provide an additional layer of security by allowing administrators to enforce network-level security policies, restrict unauthorized access, and protect against malicious activity within the Azure environment.

By associating NSGs with virtual networks and subnets, you can create a set of rules to control network traffic and apply security policies across multiple resources. This allows for fine-grained control over network communication and helps ensure the security and integrity of your Azure infrastructure.</p>

<h4>Process for Setting Up Network Security Groups:</h4>

1. In the command-line inside of our Microsoft Windows VM, type “ping 10.0.0.5 -t”
The “-t” means to continuously, indefinitely “ping”.

2. We can see in Wireshark via the Source/Destination & Request/Reply sections that our Windows VM and Linux VM keep talking to each other constantly & continuously 

<br>

<img src="https://i.imgur.com/iOAD7dS.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<br>

We’re now going to place a Network Security rule inside of Azure to stop ICMP traffic: 

3. Navigate to Azure (outside of Windows VM) > Select 'Virtual Machines' > Select 'VM2-Linux'
4. Select ‘Networking’ on the left-hand bar > Click ‘Add Inbound Security Rule’
5. Our goal is to cease ICMP traffic from anywhere and everywhere. Thus, we leave the Source with “Any” and Destination “Any”. Port ranges don’t need to be specified since we want to stop it from everywhere. Under ‘Protocol’ select ‘ICMP’. The action should be ‘Deny’ since we want to cease traffic.
6. The priority number is important. We want to give it top priority. We want this rule to be honored and not allow other rules to tamper with it. Thus, we give it the earliest/smallest number so it’s placed at the top of the hierarchy. Anything above 300 is good as an extra measure in avoiding the SSH rule. In this case we will type ‘200’
7. We will give it the name ‘DenyICMPAnywhere’ and ‘Add’

<br>

<img src="https://i.imgur.com/TvpoiEQ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/LzBVxJA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<br>

8. <p>We can now see our rule has been added. We will navigate back inside the Microsoft Windows VM and see the traffic on Wireshark. We see that the Request has 'Timed Out'. This means that there is a Firewall preventing the inbound traffic.</p>

<Strong>Why block ICMP Traffic?</strong>

<p>Blocking ICMP (Internet Control Message Protocol) traffic on a firewall is often done for security reasons. ICMP can be misused for network reconnaissance, such as ICMP-based scanning or ICMP redirect attacks, which can compromise network security. By blocking ICMP traffic, potential vulnerabilities and information leakage can be mitigated, reducing the attack surface and enhancing the overall security posture of the network. However, it's important to note that blocking ICMP may also hinder legitimate network troubleshooting and diagnostic capabilities, so firewall rules should be carefully configured based on the specific security requirements and operational needs of the network.</p>

<img src="https://i.imgur.com/CNVhjGM.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<br>

Now that we have witnessed what Network Security Groups can do, we will allow for inbound ICMP traffic again:

9. Navigate back to Azure Portal > Select 'Virtual Machines' > Select 'VM2-Linux' > 'Networking' > Delete our Network Security Rule
10. Now that we have deleted this rule, the ‘ping 10.0.0.5 -t’ command should start working again. Inbound traffic is being permitted again since we deleted the rule that was preventing it. If we navigate back to our command line, we can see it being allowed again
11. Type ‘Control C’ in the command-line to stop the constant pinging 

<br>

<img src="https://i.imgur.com/lIsrvNG.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/Rz3T7sE.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<br>
<br>

<h3>5: Test Out Various Other Networking Protocols (SSH, DHCP, DNS, RDP)</h3>

<h4>SSH Protocol:</h4>

<strong>What is SSH protocol?</strong>

<p>SSH protocol allows us to access another computer's command-line. SSH (Secure Shell) is a cryptographic network protocol that allows secure remote access and communication between computers. It provides a secure way to log into a remote system over an untrusted network, encrypting the connection and preventing unauthorized access.</p>

<p>SSH is important because it enables secure remote administration, file transfers, and tunneling of other network services. It ensures confidentiality, integrity, and authentication, making it a crucial protocol for secure remote access and management of systems and servers.</p>

<h4>Process for Testing SSH Protocol:</h4>
  
1. Navigate to Microsoft Windows VM > Open Wireshark > filter by SSH by typing "SSH" into search bar > press "Enter" key

<img src="https://i.imgur.com/BLZzzlp.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<br>

<p>Using the command-line (Powershell) in the Windows VM, we want to “SSH” into our Ubuntu Linux VM. Doing so will give us access to the Linux VM command-line. We will need the Private IP Address again (example: 10.0.0.5) and the username/password we set up when originally creating our Linux VM back in Step 1.</p>

2. In Powershell, type “ssh [username]@[privateipaddress]”; For this example: "ssh darinstathos@10.0.0.5"
3. When asked whether we want to continue connecting, type “Yes”

<p>**Sidenote: just by us trying to access the Linux command-line, we can already see some traffic being created inside of Wireshark</p>

4. Type in the password that you created and hit the 'Enter' key. The characters will not show but trust that the command-line is aware of you typing your password

<img src="https://i.imgur.com/Xt8Y2Pc.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<br>

5. We can now see SSH traffic being generated in Wireshark. Inside the command-line, the green colored text signifies that we are inside our Linux VM command-line
6. Type ‘Exit’ and get out of our Linux VM and back into our Windows VM. We won’t do or change anything inside of our Linux command-line for now

<img src="https://i.imgur.com/8GzpJGp.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/HUdqNEC.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<br>

<h4>DHCP Protocol:</h4>

<strong>What is DHCP protocol?</strong>

<p>DHCP (Dynamic Host Configuration Protocol) is a network protocol that automatically assigns IP addresses to devices on a network. It is important because it simplifies the process of connecting devices to a network by eliminating the need for manual IP address configuration. DHCP ensures that devices can easily join a network and communicate with other devices without conflicts, making network setup and management more efficient and convenient.</p>

<p>What we're going to do right now is request a new IP address from the DHCP server installed within Microsoft Azure and observe the traffic that happens on Wireshark:</p>

<h4>Process for Testing DHCP Protocol:</h4>

1. Within Microsoft Windows VM > open 'Wireshark' > filter by 'DHCP' in the search bar on top > press "Enter" key

<br>

<img src="https://i.imgur.com/hHLVIu9.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<br>

2. In the command-line (Powershell), type “ipconfig /renew” 

<p>The command "ipconfig /renew" is like asking the network to give your device a new address. It tells your computer to request a fresh IP address from the DHCP server, which is responsible for assigning IP addresses on the network. This command is useful when you want to refresh your network connection or resolve connectivity issues by obtaining a new IP address.</p>

<p>**Side note: We may temporarily lose connection to our VM as we are assigned a new IP address from the DHCP server</p>

3. We can see on Wireshark that some traffic was generated once we requested a new IP address

<img src="https://i.imgur.com/IfjmIg8.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>


<br>

<h4>DNS Protocol:</h4>

<strong>What is DNS protocol?</strong>

<p>DNS (Domain Name System) is a protocol that translates human-readable domain names, such as www.google.com, into IP addresses, which are the numerical addresses used by computers to identify each other on a network. The DNS protocol is responsible for resolving domain names to their corresponding IP addresses and vice versa. The DNS protocol plays a crucial role in enabling the internet to function by providing a distributed and hierarchical system for domain name resolution. It helps users access websites and other network resources using familiar domain names, without needing to remember and enter the underlying IP addresses.</p>

We're going to use the Microsoft Windows VM command-line to find IP addresses of popular sites on the internet and witness the traffic it generates on Wireshark:

<h4>Process for Testing DNS Protocol:</h4>

1. Inside Microsoft Windows VM, navigate to Wireshark > filter by "DNS" or "tcp.port == 53" in the search bar > press "Enter" key

<img src="https://i.imgur.com/uOXcmP1.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<br>

2. In the command-line (Powershell), type “nslookup www.google.com”

<p>The "nslookup" command is like a detective tool for finding information about domain names and IP addresses. When you use the "nslookup" command and provide a domain name or IP address, it helps you discover details like the corresponding IP address, domain name ownership, and DNS configurations. It's a useful tool for troubleshooting network issues and checking the status of DNS servers.</p>

3. Within Wireshark, we see with the source (our VM IP Address) & destination (DNS Server) communicating with each other. We can see all of the information about Google the DNS Server extracted, and Google's IP Address appears in the command-line.

<img src="https://i.imgur.com/9euDERZ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>


<br>

<h4>RDP Protocol:</h4>

<strong>What is RDP Protocol?</strong>

<p>RDP (Remote Desktop Protocol) is a technology that allows you to remotely access and control a computer or server from a different location. It enables you to see and interact with a remote computer's desktop as if you were sitting in front of it. RDP is commonly used for remote administration, remote support, and accessing resources on a remote computer securely and conveniently.</p>

<h4>Process for Testing RDP Protocol:</h4>

1. Inside Microsoft Windows VM, navigate to Wireshark > filter by "tcp.port == 3389" in the search bar (TCP Port 3389 is the port using for Remote Desktop Protocol) > press "Enter" key
2. We can see that it’s already spamming traffic within Wireshark because we are currently using RDP since our physical machine is utilizing Remote Desktop Connection to operate our Virtual Machine 

<br>

<img src="https://i.imgur.com/B0UkLDR.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
