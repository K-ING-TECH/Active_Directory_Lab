# Active_Directory_Lab
This repository provides a detailed step-by-step guide for setting up an Active Directory lab environment using Oracle VM VirtualBox. It includes instructions for configuring a domain controller, DHCP, DNS, and network address translation (NAT) to create a fully functional AD lab


# Setting Up Your Active Directory Lab: A Detailed Step-by-Step Guide

Welcome! In this guide, we'll set up an Active Directory lab environment. We’ll create a domain controller with two network interfaces: one external (to get DHCP from your router) and one internal (to manage your lab devices). Let’s dive in using Oracle VM VirtualBox!

# Prerequisites
- Oracle VM VirtualBox installed
- Windows Server ISO
- Basic networking and virtualization knowledge
  
# Project Overview
<img src="https://i.imgur.com/1gsZBN1.png" width="600" alt="Project Overview">



# Step 1: Create a New Virtual Machine
1. **Open Oracle VM VirtualBox**.
2. **Click** the `New` icon (a blue star) at the top of the VirtualBox Manager window.
3. **Name your VM** (e.g., `TestWindowsServer1`).
4. **Select the ISO**:
   - Click the folder icon next to the ISO Image field.
   - **Navigate** to your Windows Server ISO file and **select** it.
   - Click `Open`.
5. **Configure Resources**:
   - **Click** `Next`.
   - On the `Memory size` page, drag the slider to allocate 16 GB of RAM (or type `16384`).
   - **Click** `Next`.
   - On the `Hard disk` page, select `Create a virtual hard disk now`.
   - **Click** `Create`.
   - On the `Hard disk file type` page, select `VDI (VirtualBox Disk Image)`.
   - **Click** `Next`.
   - On the `Storage on physical hard disk` page, select `Dynamically allocated`.
   - **Click** `Next`.
   - On the `File location and size` page, leave the default settings and **click** `Create`.
6. **Complete the Setup Wizard**.

# Step 2: Configure VM Settings
1. **Right-click** your new VM in the list and select `Settings`.
2. **General**:
   - Click the `Advanced` tab.
   - Set `Shared Clipboard` to `Bidirectional`.
   - Set `Drag’n’Drop` to `Bidirectional`.
3. **Network**:
   - Click the `Network` tab.
   - **Adapter 1**:
     - Set `Attached to` to `NAT`.
   - **Adapter 2**:
     - Click the `Enable Network Adapter` checkbox.
     - Set `Attached to` to `Internal Network`.
     - Set `Name` to `internal`.
4. **Click** `OK` to save the settings.

# Step 3: Install Windows Server
1. **Start the VM** by **double-clicking** it in the list.
2. **Follow the Windows Server installation steps**:
   - Select `Language`, `Time and currency format`, and `Keyboard or input method`.
   - **Click** `Next`.
   - **Click** `Install now`.
   - Select `Windows Server 2019 Datacenter (Desktop Experience)` and **click** `Next`.
   - Accept the license terms and **click** `Next`.
   - Select `Custom: Install Windows only (advanced)`.
   - Select the disk and **click** `Next`.
3. **Set up an Administrator Password**:
   - When prompted, enter a password and confirm it.
   - **Click** `Finish`.

# Step 4: Post-Installation Configuration
1. **Install Guest Additions**:
   - In VirtualBox, **click** `Devices` in the menu bar.
   - Select `Insert Guest Additions CD Image`.
   - Inside the VM, **open File Explorer** (right-click Start > `File Explorer`).
   - **Double-click** the `VirtualBox Guest Additions` CD drive.
   - **Run the installer** (VBoxWindowsAdditions-amd64).
   - Follow the prompts and **reboot** the VM when prompted.
2. **Rename the Server**:
   - Right-click `This PC` on the desktop and select `Properties`.
   - **Click** `Rename this PC (advanced settings)` on the right side.
   - **Click** `Change`.
   - Enter `DC01` in the `Computer name` field and **click** `OK`.
   - **Restart** the VM when prompted.

# Step 5: Network Configuration
1. **Identify Network Adapters**:
   - Right-click the network icon in the taskbar (bottom right) and select `Open Network & Internet settings`.
   - **Click** `Change adapter options`.
   - Identify the external adapter (DHCP assigned) and internal adapter (APIPA address).
2. **Configure Internal Adapter**:
   - Right-click the internal adapter and select `Properties`.
   - Select `Internet Protocol Version 4 (TCP/IPv4)` and **click** `Properties`.
   - Select `Use the following IP address`.
   - Enter the following:
     - IP Address: `172.16.0.1`
     - Subnet Mask: `255.255.255.0`
     - Default Gateway: Leave blank.
     - Preferred DNS Server: `127.0.0.1`
   - **Click** `OK` and then **Close**.

# Step 6: Install Active Directory Domain Services
1. **Open Server Manager** (Start > `Server Manager`).
2. **Add Roles and Features**:
   - Click `Manage` > `Add Roles and Features`.
   - Click `Next` on the Before You Begin page.
   - Select `Role-based or feature-based installation` and **click** `Next`.
   - Select the server (DC01) and **click** `Next`.
   - Select `Active Directory Domain Services` and **click** `Next`.
   - **Click** `Add Features` when prompted, then **Next**.
   - Click `Next` until you reach the confirmation page, then **click** `Install`.
3. **Promote the Server**:
   - Click the flag icon with a yellow warning triangle in Server Manager.
   - Click `Promote this server to a domain controller`.
   - Select `Add a new forest`.
   - Enter `mydomain.com` as the Root domain name and **click** `Next`.
   - Follow the wizard steps to set up the forest and domain.

# Step 7: Configure DHCP
1. **Add the DHCP Role**:
   - Open **Server Manager**.
   - Click `Manage` > `Add Roles and Features`.
   - Select `DHCP Server` and **click** `Next`.
   - Click `Add Features` when prompted, then **Next**.
   - Follow the prompts to complete the installation.
2. **Configure DHCP**:
   - Open `Server Manager` > `Tools` > `DHCP`.
   - Expand the server node and right-click `IPv4`.
   - Select `New Scope`.
   - **Follow the New Scope Wizard**:
     - Name: `Internal Network`.
     - IP Range: Start `172.16.0.100`, End `172.16.0.200`.
     - Subnet Mask: `255.255.255.0`.
     - Leave exclusions blank.
     - Lease Duration: Default 8 days.
     - Router: `172.16.0.1`.
     - DNS Server: `172.16.0.1`.
   - **Activate the Scope**.

# Step 8: Enable Network Address Translation (NAT)
1. **Add the Remote Access Role**:
   - Open **Server Manager**.
   - Click `Manage` > `Add Roles and Features`.
   - Select `Remote Access` and **click** `Next`.
   - Click `Next` until you reach the Role Services page.
   - Select `Routing` and **click** `Next`.
   - Follow the prompts to complete the installation.
2. **Configure Routing and Remote Access**:
   - Open `Server Manager` > `Tools` > `Routing and Remote Access`.
   - Right-click the server (DC01) and select `Configure and Enable Routing and Remote Access`.
   - Select `Network Address Translation (NAT)`.
   - Follow the wizard to enable NAT for the internal network.

# Step 9: Create Organizational Units and Users
1. **Open Active Directory Users and Computers** (Start > `Windows Administrative Tools` > `Active Directory Users and Computers`).
2. **Create a New Organizational Unit (OU)**:
   - Right-click the domain name (mydomain.com) > `New` > `Organizational Unit`.
   - Name it `Admins` and **click** `OK`.
3. **Create a New Admin User**:
   - Right-click the `Admins` OU > `New` > `User`.
   - Enter the user details (e.g., First Name: Admin, Last Name: User, User logon name: admin).
   - Set a password and uncheck `User must change password at next logon`.
   - **Click** `Next`, then `Finish`.
4. **Add the User to the `Domain Admins` Group**:
   - Right-click the new user > `Properties` > `Member Of` tab.
   - **Click** `Add`.
   - Enter `Domain Admins` and **click** `Check Names`.
   - **Click** `OK`, then `Apply`, and `OK`.

# Step 10: Join a Client Machine to the Domain
1. **Create a New VM** for a Windows 10 client.
2. **Configure the Network**:
   - Open the VM settings.
   - Go to `Network`.
   - Attach the adapter to the `internal` network.
3. **Install Windows 10**:
   - Start the VM and

 follow the Windows 10 installation steps.
4. **Join the Domain**:
   - Right-click `This PC` > `Properties` > `Rename this PC (advanced settings)`.
   - **Click** `Change`.
   - Select `Domain` and enter `mydomain.com`.
   - **Enter domain admin credentials** to join the domain.
   - Restart the VM when prompted.

# Step 11: Verify Configuration
1. **Check DHCP Leases**:
   - Open `Server Manager` > `Tools` > `DHCP`.
   - Expand the server node > `IPv4` > `Address Leases`.
   - Verify the client has received an IP address.
2. **Verify DNS Resolution**:
   - On the client machine, open `Command Prompt` (right-click Start > `Command Prompt`).
   - Type `ipconfig /all` to check the network configuration.
   - Type `ping mydomain.com` to verify DNS resolution.
3. **Check Active Directory**:
   - Open `Active Directory Users and Computers`.
   - Expand the `Computers` container to ensure the client machine is listed.

# Conclusion
Congratulations! 🎉 Your Active Directory lab environment is now set up and ready to use. This setup is perfect for testing and learning Active Directory configurations and management in a controlled environment.

Feel free to review this detailed guide and let me know if you need any further adjustments! Once you’re satisfied, you can post this documentation on your GitHub.
