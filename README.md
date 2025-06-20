# Azure-VM-Firewall-Port-Access-



![image](https://github.com/user-attachments/assets/6c36f9e7-7d44-4ad6-82d2-884cba816a1e)

<h2> Objective </h2>


 This project simulates a help desk request where a user (or an application) on an Azure VM needs specific network access (e.g., to run a simple web server or connect to a service), and you, as the help desk, need to configure the VM's firewall to allow this. You'll then test the access and demonstrate how to troubleshoot if it fails due to firewall rules.






- Networking Fundamentals: Shows understanding of basic firewall concepts and port numbers.
- Common Request: Users frequently need specific ports opened for applications.
- Troubleshooting: Demonstrates diagnosing network connectivity issues.
- Azure VM Networking: Practical experience with Azure Network Security Groups (NSGs).
- Cost-Effective: Uses a small Azure VM and minimal other services.


<h2>Azure Services Used: </h2>

- Azure Virtual Machine (VM): A Windows VM to simulate a user's machine.
- Azure Network Security Group (NSG): The Azure-level firewall for the VM.
- Azure Resource Groups: For organization.







<h2>Part 1: Setting up the Azure Virtual Machine </h2>



- First you'll Log in to Azure Portal and create a Resource Group: 
    - Search for "Resource groups" -> "+ Create"
    - Resource group name: HelpDeskVMRG
    - Region: Choose a region close to you (e.g., East US).
    - "Review + create" -> "Create".
 
![image](https://github.com/user-attachments/assets/60226cc1-26fe-41e8-b035-2a8fef477c02)




- Create a Windows Virtual Machine (VM): HelpDeskVM
    - Search for "Virtual machines" -> "+ Create" -> "Azure virtual machine".
    - Resource group: HelpDeskVMRG
    - Virtual machine name: "HelpDeskVM"
    - Region: Same as RG.
    - Image: Windows 10 Pro or Windows Server 2019 Datacenter (Server is generally easier for testing in Azure).
    - Size: Standard_B1s (CRITICAL for cost).
    - Administrator account: Username: AzureAdmin, create a strong password (write it down!).
    - Inbound port rules: Select Allow selected ports, check RDP (3389).
    - "Review + create" -> "Create".
 
![image](https://github.com/user-attachments/assets/a745996a-235d-409d-992d-4d3de9d3a30f)


 - After you create it wait for deployment to complete.
    - Connect to your Azure VM via remote desktop connection:
    - Open the file, enter AzureAdmin username and your password. You should be on the VM's desktop.

![image](https://github.com/user-attachments/assets/12794754-0fff-42c5-ab3a-3ee2ba2866b4)





<h2>Part 2: Configure and Troubleshoot Firewall Rules</h2>

   - Inside your HelpDeskVM install a simple web server: We'll use IIS (Internet Information Services) as a simple example.
      - Open Control Panel, click on uninstall program under "Programs"
      - click on "Turn Windows features on or off".




  ![image](https://github.com/user-attachments/assets/5c192a9b-ce65-4b0d-a462-c28f1958fdb9)
    
        
   
   ![image](https://github.com/user-attachments/assets/d3a2cdd6-6d05-4aa9-8605-0d2a5b2d9607)

 

 
 - Find and check "Internet Information Services". Click "OK". Let it install.
 - Open a browser inside the VM and go to http://locahost. You should see the default IIS welcome page. This confirms IIS is running locally.

![image](https://github.com/user-attachments/assets/ca3868bc-a205-4687-8d48-1747e194d517)


 - On your host machine (your local computer, not the VM), open a web browser.
 - Find your VM's Public IP address in the Azure Portal (on the HelpDeskVM Overview blade).
 - Try to access the web server: http://<YOUR_VM_PUBLIC_IP_ADDRESS>.



![image](https://github.com/user-attachments/assets/2ed3ccbc-ab68-47da-a189-538472c05cdc)

![image](https://github.com/user-attachments/assets/dcdafe6d-d9f3-4685-a0c5-6d1f44c998a6)


- Expected Result: It will likely time out or show "This site can't be reached" (unless RDP somehow opened port 80 by default, which is unlikely for a client OS, or if a Windows Server image has stricter default rules). This is because port 80 is blocked by Azure's Network Security Group (NSG) by default. What were going to do now is configure Azure Network Security Group (NSG) to Allow Access



On your host machine, in the Azure Portal go to your HelpDeskVM resource. Then in the left-hand menu, under "Networking", click "Networking".

![image](https://github.com/user-attachments/assets/f582805c-3136-4cb2-a198-3f439f225ab3)



- You'll see the Network Security Group (NSG) associated with your VM. Click on its name (e.g., HelpDeskVM-nsg).


![image](https://github.com/user-attachments/assets/59c35117-471a-476f-975a-7390a2502eae)



- In the NSG blade, under "Settings", click "Inbound security rules".
   - Click "+ Add".
   - Source: Any (for simplicity; in production, you'd specify an IP range).
   - Source port ranges: *
   - Destination: Any
   - Destination port ranges: 80 (This is the standard HTTP port for web servers).
   - Protocol: TCP
   - Action: Allow
   - Priority: Choose a number lower than 65500 (e.g., 100). Lower numbers are higher priority.
   - Name: AllowHTTP (or AllowPort80)
   - Click "Add".


![image](https://github.com/user-attachments/assets/8a8d8a6a-b576-4358-bdb2-e62f592a09f0)


On your host machine, open a web browser.
Go to http://<YOUR_VM_PUBLIC_IP_ADDRESS>.
Expected Result: You should now see the default IIS welcome page! This confirms the NSG rule is working. Take a screenshot of this successful access. :)


![image](https://github.com/user-attachments/assets/4c82677f-cc30-477a-af15-f420a6233161)

