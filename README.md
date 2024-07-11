<p align="center">
<img src="https://github.com/SpyderSec30/Active-Directory-within-Azure/assets/174487140/7ddd433c-a1ae-4f50-8075-20cd11ef33c0"/>
</p>

<h1>Network Security Groups and Networking Protocols</h1>

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Computers)
- Remote Desktop Protocol (RDP)
- Wireshark
- ICMP
- SSH
- DHCP
- DNS

<h2>Getting Started with ICMP</h2>
<p>
This lab requires two virtual machines. One is going to be a Windows Computer and the other will be a Ubuntu Linux Server. Creating a VM in Azure is simple, just create a Resource Group to hold both machines. Then type "Virtual Machine" in the search bar, press the create button and go through the options. In order for the two machines to see each other on the network. I'll make sure there both in the same Resource group and on the same network subnet. I will then download Wireshark on the Windows machine so that I can view the network traffic of the two machines.
</p>

<p>
First lets get the private IP addresses of both machines. We can do this in the Azure portal by selecting each VM to go into its settings.<br></br>
  
<img src="https://github.com/SpyderSec30/Azure-Network-Security-Groups-and-Network-Protocols/assets/174487140/50dfac21-79cd-4c64-a052-7e2dbb0deb37"/><br></br>




<h4>Private IPs</h4>

- VM1: 10.0.0.4
- VM2: 10.0.0.5

<p>
Now lets open Powershell and ping VM2 (linux) from VM1 (Windows) then observe the traffic on Wireshark. To view ICMP traffic in Wireshark, Select the Adapter, and type ICMP in the filter line at the top. After that type "ping 10.0.0.5" in Powershell.

<h4>Before "ping" command</h4>
<img src="https://github.com/SpyderSec30/Azure-Network-Security-Groups-and-Network-Protocols/assets/174487140/728151a8-15c1-4efa-a29e-7b1fd67d2561"/><br></br>

<h4>After "ping" command</h4>
Wireshark allows you to not only view the network traffic but it will also allow you to open each packet and analyze the other protocols associated with the packet.<br></br>
<img src="https://github.com/SpyderSec30/Azure-Network-Security-Groups-and-Network-Protocols/assets/174487140/75a82598-5e80-4426-9f64-beb1e5b8fbe2"/><br></br>
</p>


<h2>Network Security Groups</h2>
<p>
Now we will configure the VM2 NSG to block ICMP messages. To view the traffice stop in real time I will use the "ping -t" command. By default Windows only sends four ICMP requests, but the -t switch will send an unlimited amount of request until it is stopped manually with ctrl + c.<br></br>

<img src="https://github.com/SpyderSec30/Azure-Network-Security-Groups-and-Network-Protocols/assets/174487140/56dcf026-cab7-4c84-a4df-07189697291b"/><br></br>

As you can see up above that the number of replies went from four to many many more. You'll see 8 in Wireshark because with each request comes a reply message. Now lets block ICMP from VM2's NSG: 

<ol>
  <li>Type Network Security Groups in the search bar and select VM2-nsg</li><br></br>
  <img src="https://github.com/SpyderSec30/Azure-Network-Security-Groups-and-Network-Protocols/assets/174487140/6ba263d5-3f6c-4a9a-ad24-fb36dacd8a04"/><br></br>

  <li>From this page drop down settings and select "Inbound security rules"</li><br></br>
  <img src="https://github.com/SpyderSec30/Azure-Network-Security-Groups-and-Network-Protocols/assets/174487140/1542ecb3-87b7-4b24-b1b8-33ffd0bfa7b5"/><br></br>

  <li>On the Inbound page select Add</li><br></br>
  <img src="https://github.com/SpyderSec30/Azure-Network-Security-Groups-and-Network-Protocols/assets/174487140/953e53bf-9e43-4c24-bb27-132635f023ed"/><br></br>

  <li>Apply the following and press add:</li>

  - Protocol = ICMPv4
  - Action = Deny
  - Priority = 200
  - Name = Deny ICMP<br></br>

  <img src="https://github.com/SpyderSec30/Azure-Network-Security-Groups-and-Network-Protocols/assets/174487140/9000ada7-0e14-4d05-acb3-e2f7dc3341a1"/><br></br>

  <li>Once the rules is created and ICMPs are blocked. You can see that in Wireshark that there is a "no response found!" message also back in Powershell it says "Request timed out</li><br></br>
  <img src="https://github.com/SpyderSec30/Azure-Network-Security-Groups-and-Network-Protocols/assets/174487140/226a2b2c-edbd-486e-a7f8-e2f1df475483"/><br></br>
  
  <img src="https://github.com/SpyderSec30/Azure-Network-Security-Groups-and-Network-Protocols/assets/174487140/ac13dad2-28b6-4f24-b4b3-2a1a86b8d7eb"/><br></br>
  
  

  <li>To allow the traffic to come back through just go through the same process. Edit the rule and select allow instead of deny and as you can see ICMP traffic is back flowing through the network.</li><br></br>
  <img src="https://github.com/SpyderSec30/Azure-Network-Security-Groups-and-Network-Protocols/assets/174487140/ab365017-52ed-4486-8c0b-ac4e6c1db629"/><br></br>
</ol>

<h2>SSH</h2>

<p>
SSH (Secure Shell) is a network protocol that allows secure communication between two networked devices. It is typically used to access and manage remote servers securely over an unsecured network. SSH provides strong authentication and encrypted data transmission, ensuring that sensitive information, such as login credentials and commands, remains protected from eavesdropping or interception.
</p>

<p>
You can SSH into a computer by typing "ssh username@ipaddress" inside Powershell. To connect, you will need the login credentials of the user your connecting to. Once I make the connection you can view the traffic in Wireshark by typing ssh in the filter. 

<img src="https://github.com/SpyderSec30/Azure-Network-Security-Groups-and-Network-Protocols/assets/174487140/ab1c174c-4429-4999-b72b-4815bc998d2f"/><br></br>

<h4>SSH traffic in Wireshark</h4>
<img src="https://github.com/SpyderSec30/Azure-Network-Security-Groups-and-Network-Protocols/assets/174487140/44df1c8c-b94b-4e49-984d-0856433bc0b4"/><br></br>

<h4>SSH uses TCP port 22</h4>
<img src="https://github.com/SpyderSec30/Azure-Network-Security-Groups-and-Network-Protocols/assets/174487140/4f71347c-c3ef-44e0-9f38-020f01e30e19"/><br></br>

<h4>SSH connection Successful</h4>
<img src="https://github.com/SpyderSec30/Azure-Network-Security-Groups-and-Network-Protocols/assets/174487140/5c81d385-f85b-4a64-a3a6-369cdac51009"/><br></br>
</p>

<h2>DHCP</h2>

<p>
DHCP (Dynamic Host Configuration Protocol) is a network protocol used to automatically assign IP addresses and other network configuration parameters to devices on a network, so they can communicate with other IP networks. 
</p>

<p>
To view a computer's IP information just type ipconfig in Powershell. We can force some DHCP traffic by typing ipconfig /renew
</p>

<img src="https://github.com/SpyderSec30/Azure-Network-Security-Groups-and-Network-Protocols/assets/174487140/9bef2741-680f-4365-a5e5-3d40b17b93f6"/><br></br>


<h4>DHCP traffic in Wireshark</h4>
<img src="https://github.com/SpyderSec30/Azure-Network-Security-Groups-and-Network-Protocols/assets/174487140/ea4eb690-d3d7-4ece-aa64-80b698b58fe4"/><br></br>

<h4>The DHCP server uses UDP port 67 and the computer requesting the IP address (client) uses UDP port 68 </h4>
<img src="https://github.com/SpyderSec30/Azure-Network-Security-Groups-and-Network-Protocols/assets/174487140/69454df1-e7df-401d-8d2b-535849aaa372"/><br></br>
</p>

<h2>DNS</h2>
<p>
DNS (Domain Name System) is a system that translates human-readable domain names into IP addresses that computers use to identify each other on the network. Kinda like a phonebook for the internet, enabling users to access websites using easy-to-remember names instead of IP addresses.
</p>

<p>
Your machine will automatically have DNS traffic running in the background that you dont even know about unless you view the data flow in Wireshark. I'm going to refresh Wireshark to clear the packets that are already there. Then I'll run the command nslookup which is used for querying the Domain Name System (DNS) to obtain domain name or IP address mapping information.

<img src="https://github.com/user-attachments/assets/0305b885-979f-4c39-abf7-ee11c8e094f9"/><br></br>

<h4>DNS traffic flow. Each line represents a query to a DNS server for www.google.com. Since this is a fresh VM it had to search outside its DNS Cache to find the site.</h4>
<img src="https://github.com/user-attachments/assets/d9b9ba20-aa6d-443f-b3d7-1d698e7c3efc"/><br></br>

<h4>DNS servers use UDP port 53</h4>
<img src="https://github.com/user-attachments/assets/1e5810c5-7e6e-4f41-b44b-240167bec650"/><br></br>
</p>

<h2>Finished</h2>
<p>
In conclusion, understanding common networking protocols such as ICMP, SSH, DHCP, and DNS is essential for anyone involved in IT, network administration, and cybersecurity. These protocols form the backbone of network communication, enabling efficient and secure data transfer, device management, and connectivity. By mastering the use of tools like ping for ICMP, ssh for secure shell access, dhclient for DHCP, and nslookup for DNS, professionals can effectively troubleshoot, secure, and optimize network performance.
</p>





















</p>
