# Active-Directory lab using VirtualBox
This guide walks you through setting up an isolated lab environment using VirtualBox to install a Windows Server Domain Controller and a Windows 10/11 client joined to the domain.


# Overview
You will:

- Set up VirtualBox networking
- Install Windows Server (for Domain Controller)
- Promote the server to a Domain Controller (Active Directory)
- Set up a Windows Client (Windows 10/11)
- Join the client to the domain
- Apply basic GPO


##  Step 1: Configure VirtualBox Network

### Create Internal Network

1. Open VirtualBox → `File` → `Host Network Manager`
2. Click `Create`. Enable **DHCP** if desired
3. Alternatively, configure each VM's `Adapter 1` as **Internal Network** named (LabNet)


## Step 2: Create the Windows Server VM (DC01)

### VM Settings

- **Name**: `DC01`
- **Type**: Microsoft Windows
- **Version**: Windows 2019 or 2022 (64-bit)
- **RAM**: 4+ GB  
- **CPU**: 2 cores  
- **HDD**: 60 GB (VDI, dynamically allocated)

### ISO Attachment

- Mount the Windows Server ISO in the optical drive (you can find this by a quick search on google)

### Network Settings

- **Adapter 1**: Internal Network (`LabNet`)

Install the OS after booting.


## Step 3: Configure Windows Server

1. Set a strong **Administrator** password
2. **Configure Static IP**:

          IP Address: 192.168.1.10
          
          Subnet: 255.255.255.0
          
          Gateway: 192.168.1.1 (optional)
          
          DNS: 127.0.0.1

3. **Rename the Computer**:

Server Manager → Local Server → Computer Name → Change → DC01


## Step 4: Install Active Directory Domain Services
  1. Open Server Manager

  2. Click Manage → Add Roles and Features

  3. Follow the wizard:

    Installation Type: Role-based

    Server Selection: Local server

    Roles: Check Active Directory Domain Services

    Accept features prompt

    Finish and restart if needed



## Step 5: Promote to Domain Controller
1. Back in Server Manager, click the notification flag(at the top) → Promote this server to a domain controller

2. Select:
 
  Add a new forest  
  Root domain: lab.local

3. Set the DSRM password

4. Continue with defaults → Install

## Step 6: Create a Domain User

1. Open Active Directory Users and Computers

2. Right-click your domain → New → User

3. Example: TestUser

4. Set password and uncheck "User must change password at next login"

![Alt Text](https://github.com/JamahdPerry99/Active-Directory-/blob/47ba64b869153639c573cb03263b4463c7e55cc0/Creating%20user.png)

## Step 7: Create the Windows Client VM (WIN10CLIENT)
VM Settings
- Name: WIN10CLIENT

- Type: Microsoft Windows

- Version: Windows 10/11 (64-bit)

- RAM: 2–4 GB

- HDD: 40–60 GB

ISO Attachment
- Mount Windows 10 or 11 ISO

Network Settings
- Adapter 1: Internal Network (LabNet)


## Step 8: Configure Windows Client
1. Complete the Windows installation and log in

2. Set Static IP:
- IP Address: 192.168.1.20  
- Subnet: 255.255.255.0  
- Gateway: 192.168.1.1 (optional)  
- DNS: 192.168.1.10 (Domain Controller IP)

![Alt Text](https://github.com/JamahdPerry99/Active-Directory-/blob/9059b7e476444099e392dad53cf5350e2a5a2edb/setting%20up%20IP%20settings%20on%20window%20client.png)

  ## Step 9: Join Client to Domain
1. Go to:
   Settings → System → About → Rename this PC (advanced)

2. Click Change

3. Select Domain, enter: lab.local

4. Enter credentials:

  - Username: Administrator  
  - Password: This was set earlier on DC01
5. Restart

## Step 10: Log in as Domain User

1. At login screen → Click Other user

2. Enter:
  - Username: testuser
  - Password: (password you created)

## Applying GPOs

# Step 1: Applying Basic GPOs

1. Go to Server Manager
2. Select tools, then Group Policy Management

# Step 2: Create a New GPO

Step 2: Create a New GPO

1. Right-click lab.local (or an OU like Computers)

2. Select “Create a GPO in this domain, and Link it here…”

3. Name your GPO (e.g., Disable Control Panel & password complexity)

![Alt Text](https://github.com/JamahdPerry99/Active-Directory-/blob/6fd471363a2eaee806303de7d33dbceab4319681/creating%20GPO%20policy%20.png)

# Step 3: Edit the GPO

1. Right-click your new GPO → Edit

2. Navigate using one of the following to disable control panel & password complexity for users:

   - User Configuration → Administrative Templates → Control Panel → Prohibit access to Control Panel and PC settings → Set to Enabled

![Alt Text](https://github.com/JamahdPerry99/Active-Directory-/blob/cbf3b4eed605d80c1f4a293187789f28f0e78e25/Enabling%20GPO%20policy%20for%20control%20panel.png)

   -  Computer Configuration → Windows Settings → Security Settings → Account Policies → password policy

![Alt Text](https://github.com/JamahdPerry99/Active-Directory-/blob/cbf3b4eed605d80c1f4a293187789f28f0e78e25/enabling%20password%20complexity%20on%20the%20computer%20configuration.png)


# step 4: apply GPO and force Update

1. Run the following command to update policy from the domain "gpupdate /force"

![Alt Text](https://github.com/JamahdPerry99/Active-Directory-/blob/456614803f8f3ecbf650bb909aa15d131019eae1/forcing%20update%20on%20client.png)

2. Check to ensure GPOs are applied "gpresult /r" 

![Alt Text](https://github.com/JamahdPerry99/Active-Directory-/blob/456614803f8f3ecbf650bb909aa15d131019eae1/GPO%20on%20windows%20client.png)
