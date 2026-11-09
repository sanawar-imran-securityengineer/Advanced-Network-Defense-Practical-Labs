# 🛡️ MODULE 5 — Endpoint Security Lab

**A complete, hands-on, screenshot-driven walkthrough — from VM setup to automated active response**

![Module](https://img.shields.io/badge/MODULE-5-0f2027?style=for-the-badge)
![Lab](https://img.shields.io/badge/LAB-TESTED-0078D4?style=for-the-badge)
![Screenshots](https://img.shields.io/badge/SCREENSHOTS-218-2ea44f?style=for-the-badge)
![Status](https://img.shields.io/badge/STATUS-COMPLETE-44cc11?style=for-the-badge)

![Windows Server](https://img.shields.io/badge/Windows%20Server-2019-0078D4?style=flat-square&logo=windows&logoColor=white)
![Active Directory](https://img.shields.io/badge/Active%20Directory-RBAC-5E5E5E?style=flat-square&logo=microsoft&logoColor=white)
![Wazuh](https://img.shields.io/badge/Wazuh-SIEM%20%2F%20XDR-3EA7E1?style=flat-square)
![Sysmon](https://img.shields.io/badge/Sysmon-Sysinternals-00A4EF?style=flat-square)
![MITRE](https://img.shields.io/badge/MITRE-ATT%26CK-C8102E?style=flat-square)
![Kali](https://img.shields.io/badge/Kali-Linux-557C94?style=flat-square&logo=kalilinux&logoColor=white)
![VMware](https://img.shields.io/badge/VMware-Workstation-607078?style=flat-square&logo=vmware&logoColor=white)

<br>

<img src="images/ai.png" alt="Module 5 Cover" width="85%"/>

<br><br>

[📖 Overview](#-overview) • [🏗️ Lab Architecture](#️-lab-architecture) • [🧰 Tools](#-tools--technologies) • [📂 Structure](#-folder-structure) • [🧪 Walkthrough](#-lab-walkthrough) • [🎯 Outcomes](#-key-outcomes) • [📬 Contact](#-connect-with-me)

</div>

---

## 📖 Overview

This module demonstrates a **full Windows endpoint security lifecycle** in a virtual lab: building the domain, enforcing **Role-Based Access Control (RBAC)**, hardening servers with **Microsoft security baselines**, automating **patch management**, deploying a **Wazuh SIEM**, integrating **Sysmon**, mapping detections to **MITRE ATT&CK**, simulating attacks from **Kali Linux**, and finally moving from detection to **automated prevention** with Wazuh Active Response.

> 💡 Every step below includes the original lab screenshot, so the whole workflow can be reproduced.

---

## 🏗️ Lab Architecture

| 🖧 Machine | 🎭 Role | 🌐 IP Address |
| :--- | :--- | :--- |
| **DC01** | Domain Controller (`corp.local`) | `192.168.10.10` |
| **SRV01** | File Server + Patch Management (Windows Server 2019) | `192.168.10.25` |
| **IDS01** | Wazuh SIEM (indexer + manager + dashboard) | `192.168.10.60` |
| **CL01** | Windows 10 client (authorized user tests) | `192.168.10.x` |
| **CL02** | Windows 10 client (unauthorized user tests) | `192.168.10.x` |
| **Kali** | Attacker machine | `192.168.10.x` |

**👥 Test Users:** `bob` ✅ · `john` ✅ (FinanceData members) · `martin` ❌ (unauthorized) · `alice` (general user)

---

## 🧰 Tools & Technologies

| Category | Tools |
| :--- | :--- |
| 🖥️ Virtualization | VMware Workstation |
| 🏢 Identity | Active Directory, Group Policy, RBAC, NTFS + SMB permissions |
| 📏 Hardening | Microsoft Security Compliance Toolkit, Policy Analyzer, LGPO, `auditpol`, Windows Firewall |
| 🔄 Patching | BatchPatch, PsExec, Task Scheduler |
| 📡 Monitoring | Wazuh (SIEM/XDR), Sysmon, SwiftOnSecurity config |
| 🎯 Threat Mapping | MITRE ATT&CK |
| ⚔️ Offensive | Nmap, CrackMapExec, Hydra, Impacket |



## 🗺️ Phase Map

| # | Phase | Steps |
| :-: | :--- | :-: |
| 1 | [🖥️ Phase 1 — VM Setup & Domain Join](#phase1) | 1–20 |
| 2 | [🏢 Phase 2 — Active Directory & RBAC Design](#phase2) | 21–42 |
| 3 | [📁 Phase 3 — Secure File Sharing (FinanceData)](#phase3) | 43–80 |
| 4 | [📏 Phase 4 — Microsoft Security Compliance Toolkit](#phase4) | 81–108 |
| 5 | [🔄 Phase 5 — Patch Management with BatchPatch](#phase5) | 109–126 |
| 6 | [🛡️ Phase 6 — Windows Hardening](#phase6) | 127–137 |
| 7 | [📡 Phase 7 — Wazuh SIEM Deployment & Agents](#phase7) | 138–164 |
| 8 | [🔍 Phase 8 — Sysmon & Log Collection](#phase8) | 165–182 |
| 9 | [🎯 Phase 9 — Custom Detection Rules & MITRE ATT&CK](#phase9) | 183–189 |
| 10 | [⚔️ Phase 10 — Attack Simulation from Kali](#phase10) | 190–214 |
| 11 | [🚨 Phase 11 — Active Response (Detection → Prevention)](#phase11) | 215–218 |

---

## 🧪 Lab Walkthrough

> Every step has its screenshot and full documentation below.

<a id="phase1"></a>

## 🖥️ Phase 1 — VM Setup & Domain Join

<sub>Steps 1–20</sub>

### 🔹 1. SRV01 Virtual Machine Ready

- The SRV01 virtual machine (Windows Server 2019) is created in VMware and shown in its summary page.
- Its hardware is visible here: 2 GB RAM, 2 processors, a 40 GB NVMe disk and the VMnet1 network adapter.
- This machine becomes the Patch Management server and the Secure File Server for the lab.

### 🔹 2. Open Network & Internet Settings

<p align="center"><img src="images/screenshot2.png" alt="Open Network & Internet Settings" width="85%"/></p>

- Right-click the network icon in the taskbar and choose Open Network & Internet Settings on SRV01.
- This is the entry point for assigning a fixed IP address to the server.
- A static IP keeps SRV01 reachable at the same address for every other machine in the domain.

### 🔹 3. Change Adapter Options

<p align="center"><img src="images/screenshot3.png" alt="Change Adapter Options" width="85%"/></p>

- From the Network & Internet settings page, click Change Adapter Options.
- The Network Connections window opens and lists the available Ethernet adapters.
- Selecting the correct adapter is required before its IPv4 properties can be edited.

### 🔹 4. Ethernet Adapter Properties

<p align="center"><img src="images/screenshot4.png" alt="Ethernet Adapter Properties" width="85%"/></p>

- Right-click the Ethernet adapter and open its Properties dialog.
- The list of protocols and services bound to the adapter is displayed.
- Internet Protocol Version 4 (TCP/IPv4) is selected from this list in the next step.

### 🔹 5. Select IPv4 Properties

<p align="center"><img src="images/screenshot5.png" alt="Select IPv4 Properties" width="85%"/></p>

- Double-click Internet Protocol Version 4 (TCP/IPv4) to open its configuration window.
- Choose the option Use the following IP address to switch from DHCP to manual settings.
- This is where the static IP, subnet mask, gateway and DNS server are entered.

### 🔹 6. Static IP Configuration for SRV01

<p align="center"><img src="images/screenshot6.png" alt="Static IP Configuration for SRV01" width="85%"/></p>

- The values entered are IP `192.168.10.25`, subnet mask `255.255.255.0` and gateway `192.168.10.1`.
- The preferred DNS server is set to `192.168.10.10`, which is DC01, the domain controller.
- Pointing DNS to DC01 is essential so that SRV01 can find and join the corp.local domain.

### 🔹 7. Verify IP with ipconfig /all

<p align="center"><img src="images/screenshot7.png" alt="Verify IP with ipconfig /all" width="85%"/></p>

- Command Prompt is opened and `ipconfig /all` is executed on SRV01.
- The output confirms that the adapter now holds the static address 192.168.10.25.
- This proves the manual network configuration was applied correctly.

### 🔹 8. Ping Test to DC01

<p align="center"><img src="images/screenshot8.png" alt="Ping Test to DC01" width="85%"/></p>

- The command `ping 192.168.10.10` is run from SRV01 to test connectivity.
- Successful replies show that SRV01 can reach the domain controller on the network.
- Connectivity to DC01 must work before attempting to join the domain.

### 🔹 9. Open System Information

<p align="center"><img src="images/screenshot9.png" alt="Open System Information" width="85%"/></p>

- Go to Settings > System > About to view the current device name and system details.
- The default hostname is still shown, so it must be changed to SRV01.
- The Advanced system settings link on this page leads to the rename and domain options.

### 🔹 10. Advanced System Settings

<p align="center"><img src="images/screenshot10.png" alt="Advanced System Settings" width="85%"/></p>

- The System Properties window opens from the Advanced system settings link.
- Switch to the Computer Name tab, which shows the current computer name and domain or workgroup.
- Click the Change button to rename the machine and join it to the domain.

### 🔹 11. Computer Name / Domain Changes

<p align="center"><img src="images/screenshot11.png" alt="Computer Name / Domain Changes" width="85%"/></p>

- The Computer Name/Domain Changes dialog is displayed.
- The computer name is typed as SRV01 in the upper box.
- The Member of section is used next to switch the machine from a workgroup to the domain.

### 🔹 12. Join corp.local Domain

<p align="center"><img src="images/screenshot12.png" alt="Join corp.local Domain" width="85%"/></p>

- SRV01 is set as the computer name and Domain is selected with corp.local entered.
- Domain administrator credentials are requested to complete the join.
- After the success message, the VM is restarted so the new name and domain membership take effect.

### 🔹 13. Clone CL01 to Create CL02

<p align="center"><img src="images/screenshot13.png" alt="Clone CL01 to Create CL02" width="85%"/></p>

- In VMware, right-click the Windows 10 Pro machine (CL01) and go to Manage > Clone.
- Cloning is the fastest way to create the second client machine with the same software installed.
- CL02 will act as the unauthorized user workstation in the RBAC tests.

### 🔹 14. Create a Full Clone

<p align="center"><img src="images/screenshot14.png" alt="Create a Full Clone" width="85%"/></p>

- The Clone Virtual Machine wizard asks for the clone type.
- Create a full clone is selected so CL02 is a completely independent copy of CL01.
- A full clone does not depend on the original VM, which keeps the lab stable.

### 🔹 15. Name the Clone CL02

<p align="center"><img src="images/screenshot15.png" alt="Name the Clone CL02" width="85%"/></p>

- The new virtual machine is named CL02 and its storage location is chosen.
- Finishing the wizard creates the clone and adds it to the VMware library.
- CL02 is now ready to be powered on and configured with its own IP address.

### 🔹 16. Static IP Configuration for CL02

<p align="center"><img src="images/screenshot16.png" alt="Static IP Configuration for CL02" width="85%"/></p>

- The IPv4 properties of CL02 are opened and a manual address in the 192.168.10.x range is entered.
- The DNS server is set to 192.168.10.10 so the client can locate the domain controller.
- A unique IP avoids any conflict with CL01 because the machine was cloned.

### 🔹 17. Connectivity Test from CL02

<p align="center"><img src="images/screenshot17.png" alt="Connectivity Test from CL02" width="85%"/></p>

- From Command Prompt on CL02, `ping 192.168.10.10` is executed.
- Replies from DC01 confirm that CL02 is correctly connected to the lab network.
- With the network verified, the domain join can proceed.

### 🔹 18. Open Advanced System Settings on CL02

<p align="center"><img src="images/screenshot18.png" alt="Open Advanced System Settings on CL02" width="85%"/></p>

- Settings > About > Advanced system settings is opened on CL02.
- The System Properties window shows the computer name tab for this cloned machine.
- The Change button is used to rename the PC and join it to the domain.

### 🔹 19. Join CL02 to corp.local

<p align="center"><img src="images/screenshot19.png" alt="Join CL02 to corp.local" width="85%"/></p>

- The Member of Domain option is selected and corp.local is typed in.
- Credentials of a domain administrator are used to authorize the join.
- A welcome message confirms CL02 is now part of the corp.local domain.

### 🔹 20. Login as CORP\Administrator

<p align="center"><img src="images/screenshot20.png" alt="Login as CORP\Administrator" width="85%"/></p>

- After the restart, CL02 shows the Windows login screen.
- The domain account CORP\Administrator is used to sign in.
- A successful login verifies that domain authentication works on CL02.

---

<a id="phase2"></a>

## 🏢 Phase 2 — Active Directory & RBAC Design

<sub>Steps 21–42</sub>

### 🔹 21. Open Active Directory Users and Computers

<p align="center"><img src="images/screenshot21.png" alt="Open Active Directory Users and Computers" width="85%"/></p>

- On DC01, press Win+R and run `dsa.msc` to open Active Directory Users and Computers.
- The console shows the corp.local domain tree with its default containers and OUs.
- All Module 5 users, groups and organizational units are created from here.

### 🔹 22. Create the Finance OU

<p align="center"><img src="images/screenshot22.png" alt="Create the Finance OU" width="85%"/></p>

- Right-click corp.local and choose New > Organizational Unit.
- The new OU is named Finance.
- OUs allow administrators to organize accounts and apply policies per department.

### 🔹 23. Finance OU Created

<p align="center"><img src="images/screenshot23.png" alt="Finance OU Created" width="85%"/></p>

- The Finance organizational unit now appears under corp.local.
- It will contain the finance users and the security group used for RBAC.
- Keeping finance accounts in their own OU makes permission management cleaner.

### 🔹 24. Create the FinanceUsers Sub-OU

<p align="center"><img src="images/screenshot24.png" alt="Create the FinanceUsers Sub-OU" width="85%"/></p>

- Right-click the Finance OU and select New > Organizational Unit.
- The sub-OU is named FinanceUsers.
- This OU will hold the authorized finance user accounts such as bob and john.

### 🔹 25. OU Structure Verified

<p align="center"><img src="images/screenshot25.png" alt="OU Structure Verified" width="85%"/></p>

- The console now shows Finance with its FinanceUsers child OU.
- The hierarchy matches the design planned for this lab.
- The next step is to create the user accounts inside FinanceUsers.

### 🔹 26. Create User bob

<p align="center"><img src="images/screenshot26.png" alt="Create User bob" width="85%"/></p>

- Right-click the FinanceUsers OU and choose New > User.
- The first name and the logon name are both entered as bob.
- bob is one of the authorized finance users who will access the protected share.

### 🔹 27. Set bob's Password

<p align="center"><img src="images/screenshot27.png" alt="Set bob's Password" width="85%"/></p>

- The password step of the New Object wizard is displayed.
- User must change password at next logon is unchecked and Password never expires is checked.
- These settings keep the lab account stable during testing.

### 🔹 28. bob Account Created

<p align="center"><img src="images/screenshot28.png" alt="bob Account Created" width="85%"/></p>

- The final confirmation page shows the summary of the new bob account.
- Clicking Finish creates the user in the FinanceUsers OU.
- bob is now ready to be added to the FinanceData security group.

### 🔹 29. Create User john

<p align="center"><img src="images/screenshot29.png" alt="Create User john" width="85%"/></p>

- A second user, john, is created in the FinanceUsers OU using the same wizard.
- The first name and user logon name are both set to john.
- john is the second authorized finance user in the RBAC design.

### 🔹 30. Set john's Password

<p align="center"><img src="images/screenshot30.png" alt="Set john's Password" width="85%"/></p>

- The password options for john are configured the same way as bob.
- The must-change option is cleared and Password never expires is enabled.
- Finish creates the account and john appears in the OU list.

### 🔹 31. Create User martin (Unauthorized)

<p align="center"><img src="images/screenshot31.png" alt="Create User martin (Unauthorized)" width="85%"/></p>

- The user martin is created and given a different password from the finance users.
- martin is deliberately not placed in the FinanceData group.
- He represents the unauthorized user who must be denied access to the finance share.

### 🔹 32. Create User alice

<p align="center"><img src="images/screenshot32.png" alt="Create User alice" width="85%"/></p>

- A general client user named alice is created in the IT or General OU.
- The first name and logon name are entered as alice.
- alice represents a normal employee without any special finance privileges.

### 🔹 33. Set alice's Password

<p align="center"><img src="images/screenshot33.png" alt="Set alice's Password" width="85%"/></p>

- The password for alice is entered on the password page of the wizard.
- The same account options are applied as for the other users.
- This keeps all four test accounts consistent and easy to use.

### 🔹 34. alice Account Created

<p align="center"><img src="images/screenshot34.png" alt="alice Account Created" width="85%"/></p>

- The wizard summary confirms the alice account details.
- Clicking Finish adds alice to Active Directory.
- All four lab users (bob, john, martin, alice) now exist in the domain.

### 🔹 35. Create the FinanceData Security Group

<p align="center"><img src="images/screenshot35.png" alt="Create the FinanceData Security Group" width="85%"/></p>

- Right-click the Finance OU and choose New > Group.
- The group is named FinanceData with Global scope and Security type.
- This group is the core of the RBAC model because permissions will be given to the group, not to individuals.

### 🔹 36. FinanceData Group Created

<p align="center"><img src="images/screenshot36.png" alt="FinanceData Group Created" width="85%"/></p>

- After clicking OK, the FinanceData group appears inside the Finance OU.
- It is currently empty and has no members yet.
- Next, the authorized users are added to it.

### 🔹 37. Open Group Members Tab

<p align="center"><img src="images/screenshot37.png" alt="Open Group Members Tab" width="85%"/></p>

- Double-click the FinanceData group and open the Members tab.
- Click Add to select the users who should belong to this group.
- Only authorized finance employees should ever be members.

### 🔹 38. Add bob to the Group

<p align="center"><img src="images/screenshot38.png" alt="Add bob to the Group" width="85%"/></p>

- bob is typed into the Select Users dialog and Check Names is used.
- The name resolves to the correct domain account.
- Clicking OK adds bob as a member of FinanceData.

### 🔹 39. Add john to the Group

<p align="center"><img src="images/screenshot39.png" alt="Add john to the Group" width="85%"/></p>

- The same process is repeated to add john to the group.
- Check Names confirms that the account exists in corp.local.
- Both authorized users are now selected for membership.

### 🔹 40. Group Membership Confirmed

<p align="center"><img src="images/screenshot40.png" alt="Group Membership Confirmed" width="85%"/></p>

- The Members tab lists bob and john.
- Clicking Apply and OK saves the membership changes.
- martin and alice are intentionally not part of this group.

### 🔹 41. Move CL02 to the Workstations OU

<p align="center"><img src="images/screenshot41.png" alt="Move CL02 to the Workstations OU" width="85%"/></p>

- In Active Directory Users and Computers, right-click the CL02 computer object and choose Move.
- The Workstations OU is selected as the destination.
- Placing CL02 in the proper OU allows workstation policies to be applied to it.

### 🔹 42. CL02 in Workstations OU

<p align="center"><img src="images/screenshot42.png" alt="CL02 in Workstations OU" width="85%"/></p>

- The CL02 computer account is now listed inside the Workstations OU.
- This confirms the move was completed successfully.
- Phase 2 is finished and the identity structure is ready for the file-share setup.

---

<a id="phase3"></a>

## 📁 Phase 3 — Secure File Sharing (FinanceData)

<sub>Steps 43–80</sub>

### 🔹 43. Open File Explorer on SRV01

<p align="center"><img src="images/screenshot43.png" alt="Open File Explorer on SRV01" width="85%"/></p>

- File Explorer is opened on SRV01 to prepare the shared folder.
- The C: or D: drive is selected as the location for the share.
- This begins the secure file sharing configuration.

### 🔹 44. Choose the Drive

<p align="center"><img src="images/screenshot44.png" alt="Choose the Drive" width="85%"/></p>

- The local drive where the Shares folder will be created is opened.
- The existing folders on the drive are visible.
- A dedicated Shares folder keeps all shared data in one controlled place.

### 🔹 45. Create the Shares Folder

<p align="center"><img src="images/screenshot45.png" alt="Create the Shares Folder" width="85%"/></p>

- Right-click inside the drive and choose New > Folder.
- The new folder is named Shares.
- It will be the parent folder for the FinanceData share.

### 🔹 46. Create the FinanceData Folder

<p align="center"><img src="images/screenshot46.png" alt="Create the FinanceData Folder" width="85%"/></p>

- Inside Shares, a new folder named FinanceData is created.
- This folder will hold the confidential finance files.
- Access to it will be restricted using share and NTFS permissions.

### 🔹 47. Final Folder Path

<p align="center"><img src="images/screenshot47.png" alt="Final Folder Path" width="85%"/></p>

- The Explorer address bar shows the full path `C:\Shares\FinanceData`.
- This is the exact location that will be shared on the network.
- The folder is empty for now and ready to be secured.

### 🔹 48. Open Folder Properties

<p align="center"><img src="images/screenshot48.png" alt="Open Folder Properties" width="85%"/></p>

- Right-click the FinanceData folder and choose Properties.
- The Sharing tab is opened and Advanced Sharing is selected.
- This is where network share settings are controlled.

### 🔹 49. Advanced Sharing Dialog

<p align="center"><img src="images/screenshot49.png" alt="Advanced Sharing Dialog" width="85%"/></p>

- The Advanced Sharing window opens.
- It contains the option to share the folder and set permissions and caching.
- The folder is not shared yet until the checkbox is enabled.

### 🔹 50. Enable Share This Folder

<p align="center"><img src="images/screenshot50.png" alt="Enable Share This Folder" width="85%"/></p>

- Share this folder is checked in the Advanced Sharing window.
- The share name is set to FinanceData.
- This publishes the folder to the network under the name FinanceData.

### 🔹 51. Open Share Permissions

<p align="center"><img src="images/screenshot51.png" alt="Open Share Permissions" width="85%"/></p>

- Click the Permissions button to view the share-level permissions.
- By default, the group Everyone has access to the share.
- This default is too open for confidential finance data.

### 🔹 52. Remove Everyone

<p align="center"><img src="images/screenshot52.png" alt="Remove Everyone" width="85%"/></p>

- The Everyone entry is selected and removed from the list.
- Removing it blocks access for all unauthorized accounts.
- This follows the principle of least privilege.

### 🔹 53. Add the FinanceData Group

<p align="center"><img src="images/screenshot53.png" alt="Add the FinanceData Group" width="85%"/></p>

- Click Add, type FinanceData, use Check Names and press OK.
- The security group is resolved from Active Directory.
- Permissions will now be given only to this group.

### 🔹 54. Grant Full Control to the Group

<p align="center"><img src="images/screenshot54.png" alt="Grant Full Control to the Group" width="85%"/></p>

- The FinanceData group is selected and Full Control is allowed at the share level.
- Apply and OK are clicked to save the share permissions.
- Real file access is still limited later by the stricter NTFS rules.

### 🔹 55. Open Caching Settings

<p align="center"><img src="images/screenshot55.png" alt="Open Caching Settings" width="85%"/></p>

- Back in Advanced Sharing, the Caching button is clicked.
- The offline settings window appears.
- No files or programs from the shared folder are made available offline.

### 🔹 56. Caching Disabled and Applied

<p align="center"><img src="images/screenshot56.png" alt="Caching Disabled and Applied" width="85%"/></p>

- The no-offline-availability option is selected and confirmed.
- Apply and OK are clicked to finish the share configuration.
- This prevents confidential files from being cached on client machines.

### 🔹 57. Open the Security Tab

<p align="center"><img src="images/screenshot57.png" alt="Open the Security Tab" width="85%"/></p>

- In FinanceData Properties, the Security tab is selected.
- The NTFS permissions currently set on the folder are displayed.
- The Advanced button gives access to inheritance settings.

### 🔹 58. Advanced Security Settings

<p align="center"><img src="images/screenshot58.png" alt="Advanced Security Settings" width="85%"/></p>

- The Advanced Security Settings window opens for the FinanceData folder.
- It shows the permission entries and their inheritance state.
- Inheritance must be disabled to control access precisely.

### 🔹 59. Disable Inheritance

<p align="center"><img src="images/screenshot59.png" alt="Disable Inheritance" width="85%"/></p>

- Click Disable inheritance and convert inherited permissions into explicit ones.
- The folder no longer takes permissions from its parent.
- This allows unwanted entries to be removed safely.

### 🔹 60. Explicit Permissions Listed

<p align="center"><img src="images/screenshot60.png" alt="Explicit Permissions Listed" width="85%"/></p>

- After conversion, all former inherited permissions appear as explicit entries.
- These entries can now be edited or removed individually.
- The next step removes everything except the required accounts.

### 🔹 61. Remove Unnecessary Entries

<p align="center"><img src="images/screenshot61.png" alt="Remove Unnecessary Entries" width="85%"/></p>

- All entries except Administrators (Full Control) and SYSTEM are removed.
- This tightens the folder so that only essential accounts remain.
- Standard Users and other broad groups lose access.

### 🔹 62. Add the FinanceData Group in NTFS

<p align="center"><img src="images/screenshot62.png" alt="Add the FinanceData Group in NTFS" width="85%"/></p>

- Click Add and open Select a principal.
- FinanceData is typed and Check Names is used to resolve it.
- The group is about to receive NTFS permissions on the folder.

### 🔹 63. Set NTFS Permissions for the Group

<p align="center"><img src="images/screenshot63.png" alt="Set NTFS Permissions for the Group" width="85%"/></p>

- In the permission entry, Full Control is checked for the FinanceData group.
- The entry applies to this folder, subfolders and files.
- Members like bob and john now get file access through the group.

### 🔹 64. Apply the NTFS Changes

<p align="center"><img src="images/screenshot64.png" alt="Apply the NTFS Changes" width="85%"/></p>

- Apply is clicked and the permission window is confirmed with OK.
- The updated permission list is saved on the folder.
- The security tab now shows only the allowed accounts.

### 🔹 65. NTFS Permissions Finalized

<p align="center"><img src="images/screenshot65.png" alt="NTFS Permissions Finalized" width="85%"/></p>

- The final Security tab lists Administrators, SYSTEM and FinanceData.
- This is the intended RBAC model for the finance folder.
- Anyone outside these accounts, like martin, will be denied.

### 🔹 66. Open Server Manager

<p align="center"><img src="images/screenshot66.png" alt="Open Server Manager" width="85%"/></p>

- Server Manager is opened on SRV01.
- The dashboard shows the roles and features of the server.
- File and Storage Services is the next area to open.

### 🔹 67. File and Storage Services

<p align="center"><img src="images/screenshot67.png" alt="File and Storage Services" width="85%"/></p>

- The File and Storage Services section is selected on the left panel.
- The Shares page is opened to list all network shares.
- The FinanceData share created earlier appears here.

### 🔹 68. Open Share Properties

<p align="center"><img src="images/screenshot68.png" alt="Open Share Properties" width="85%"/></p>

- Right-click the FinanceData share and choose Properties.
- The properties window shows General, Permissions, Settings and Customize pages.
- The Settings page contains the access-based enumeration option.

### 🔹 69. Settings Tab

<p align="center"><img src="images/screenshot69.png" alt="Settings Tab" width="85%"/></p>

- The Settings tab of the share properties is opened.
- It lists advanced options such as enumeration and encryption.
- These options add another layer of protection to the share.

### 🔹 70. Enable Access-Based Enumeration

<p align="center"><img src="images/screenshot70.png" alt="Enable Access-Based Enumeration" width="85%"/></p>

- Enable access-based enumeration is checked.
- With this option, users cannot even see folders they have no permission for.
- This hides the finance folder from unauthorized users like martin.

### 🔹 71. Enable SMB Encryption

<p align="center"><img src="images/screenshot71.png" alt="Enable SMB Encryption" width="85%"/></p>

- Encrypt data access is checked for the share.
- SMB encryption protects the data in transit between client and server.
- It stops attackers from reading file contents through network sniffing.

### 🔹 72. Apply Share Settings

<p align="center"><img src="images/screenshot72.png" alt="Apply Share Settings" width="85%"/></p>

- Apply and OK are clicked to save the settings.
- Both access-based enumeration and encryption are now active.
- The FinanceData share is configured with defense in depth.

### 🔹 73. Share Settings Verified

<p align="center"><img src="images/screenshot73.png" alt="Share Settings Verified" width="85%"/></p>

- The share properties are reopened and confirm the saved settings.
- The enumeration and encryption options remain enabled.
- The share is ready to contain test files.

### 🔹 74. Create a Test File

<p align="center"><img src="images/screenshot74.png" alt="Create a Test File" width="85%"/></p>

- On SRV01, a new text document is created inside `C:\Shares\FinanceData`.
- The file is named Q4_Report.txt.
- It will be used to test whether permissions behave as designed.

### 🔹 75. Add Confidential Content

<p align="center"><img src="images/screenshot75.png" alt="Add Confidential Content" width="85%"/></p>

- The file is opened and a short confidential finance message is typed in.
- This simulates sensitive financial data stored on the server.
- The file is saved and closed.

### 🔹 76. Test File Saved

<p align="center"><img src="images/screenshot76.png" alt="Test File Saved" width="85%"/></p>

- The folder now shows Q4_Report.txt in the FinanceData directory.
- The file is ready for access testing from client machines.
- The RBAC tests begin in the next section.

### 🔹 77. Login to CL01 as john

<p align="center"><img src="images/screenshot77.png" alt="Login to CL01 as john" width="85%"/></p>

- On CL01, the user logs in with the domain account CORP\john.
- john is an authorized member of the FinanceData group.
- He should be able to open the finance share.

### 🔹 78. Open the Share by UNC Path

<p align="center"><img src="images/screenshot78.png" alt="Open the Share by UNC Path" width="85%"/></p>

- File Explorer is opened and the path `\\192.168.10.25\FinanceData` is typed in the address bar.
- The request is sent to SRV01 over SMB.
- The result shows whether RBAC allows the account in.

### 🔹 79. Access Granted for john

<p align="center"><img src="images/screenshot79.png" alt="Access Granted for john" width="85%"/></p>

- The FinanceData folder opens and Q4_Report.txt is visible.
- This proves that the authorized user can read the data.
- The positive test case of the RBAC design is successful.

### 🔹 80. Access Denied for martin

<p align="center"><img src="images/screenshot80.png" alt="Access Denied for martin" width="85%"/></p>

- On CL02, martin tries to open `\\192.168.10.25\FinanceData`.
- Windows shows an error saying he does not have permission to access the share.
- This is the expected result and confirms unauthorized users are blocked.

---

<a id="phase4"></a>

## 📏 Phase 4 — Microsoft Security Compliance Toolkit

<sub>Steps 81–108</sub>

### 🔹 81. Open the MSCT Download Page

<p align="center"><img src="images2/screenshot81.png" alt="Open the MSCT Download Page" width="85%"/></p>

- The Microsoft Security Compliance Toolkit download page is opened in the browser.
- This toolkit provides official security baselines for Windows and Windows Server.
- Click Download to start selecting the required packages.

### 🔹 82. Select Files to Download

<p align="center"><img src="images2/screenshot82.png" alt="Select Files to Download" width="85%"/></p>

- The list of available files is displayed on the download page.
- The Windows 10 / Windows Server 2019 baseline, Policy Analyzer and LGPO are chosen.
- Selecting only the needed files keeps the download small.

### 🔹 83. LGPO.zip Selected

<p align="center"><img src="images2/screenshot83.png" alt="LGPO.zip Selected" width="85%"/></p>

- LGPO.zip is highlighted among the available downloads.
- LGPO is the Local Group Policy Object tool used to apply baselines from the command line.
- It will be copied to System32 later so it works from anywhere.

### 🔹 84. Download Both Files

<p align="center"><img src="images2/screenshot84.png" alt="Download Both Files" width="85%"/></p>

- Click Next to start downloading the selected packages.
- The browser downloads Policy Analyzer and LGPO into the Downloads folder.
- The files will be extracted on DC01 in the next step.

### 🔹 85. Extract Policy Analyzer

<p align="center"><img src="images2/screenshot85.png" alt="Extract Policy Analyzer" width="85%"/></p>

- PolicyAnalyzer.zip is extracted into `C:\MSCT\PolicyAnalyzer`.
- The extracted folder contains the PolicyAnalyzer executable and its helper scripts.
- This tool compares baselines against the current system settings.

### 🔹 86. Locate LGPO.exe

<p align="center"><img src="images2/screenshot86.png" alt="Locate LGPO.exe" width="85%"/></p>

- The LGPO folder inside Downloads is opened and LGPO.exe is found.
- This is the version of the tool provided in the toolkit.
- It is copied to a system path in the next step.

### 🔹 87. LGPO Copied to System32

<p align="center"><img src="images2/screenshot87.png" alt="LGPO Copied to System32" width="85%"/></p>

- LGPO.exe is pasted into `C:\Windows\System32`.
- Because System32 is in the PATH, the command can be run from any directory.
- This simplifies applying the baseline from PowerShell.

### 🔹 88. Open Policy Analyzer Folder

<p align="center"><img src="images2/screenshot88.png" alt="Open Policy Analyzer Folder" width="85%"/></p>

- The folder `C:\MSCT\PolicyAnalyzer\PolicyAnalyzer_40` is opened.
- It contains PolicyAnalyzer.exe, Policy Rules and several PowerShell scripts.
- Double-click PolicyAnalyzer.exe to launch the tool.

### 🔹 89. Policy Analyzer Launched

<p align="center"><img src="images2/screenshot89.png" alt="Policy Analyzer Launched" width="85%"/></p>

- Policy Analyzer opens its main window after the executable is started.
- The window is empty because no policy rules have been added yet.
- The baseline GPOs are imported in the following steps.

### 🔹 90. Policy Analyzer Main Window

<p align="center"><img src="images2/screenshot90.png" alt="Policy Analyzer Main Window" width="85%"/></p>

- The main interface shows the options Add, Compare and View.
- These are used to import policy sets and compare them with the local system.
- The Add button begins importing the Microsoft baseline.

### 🔹 91. Add Files from GPOs

<p align="center"><img src="images2/screenshot91.png" alt="Add Files from GPOs" width="85%"/></p>

- Click File and then Add files from GPO(s).
- This allows the baseline GPO folders from the toolkit to be loaded.
- Policy Analyzer reads the backup GPO format used by Microsoft baselines.

### 🔹 92. Browse to the Baseline GPOs

<p align="center"><img src="images2/screenshot92.png" alt="Browse to the Baseline GPOs" width="85%"/></p>

- The folder `C:\MSCT\Windows 10 Version...\GPOs` is browsed.
- All GPO folders for the baseline are selected.
- These folders hold the recommended Microsoft security settings.

### 🔹 93. Select Policies to Import

<p align="center"><img src="images2/screenshot93.png" alt="Select Policies to Import" width="85%"/></p>

- The list of baseline policies is shown in the import window.
- All the policies are selected and the Import button is clicked.
- They will be saved as a single policy rules file.

### 🔹 94. Confirm the Import

<p align="center"><img src="images2/screenshot94.png" alt="Confirm the Import" width="85%"/></p>

- A dialog confirms that the selected policies are being imported.
- Click OK to continue.
- The window then asks for a name for the new rules file.

### 🔹 95. Import Completed

<p align="center"><img src="images2/screenshot95.png" alt="Import Completed" width="85%"/></p>

- The import is completed and the policies are listed in Policy Analyzer.
- The rule set is ready for comparison.
- This represents the official Microsoft recommended baseline.

### 🔹 96. Name the Rules File

<p align="center"><img src="images2/screenshot96.png" alt="Name the Rules File" width="85%"/></p>

- The file is saved with the name Server2019_Baseline.
- Naming it clearly makes it easy to find later.
- Click Compare Effective State to check the current system against it.

### 🔹 97. Compare Effective State

<p align="center"><img src="images2/screenshot97.png" alt="Compare Effective State" width="85%"/></p>

- The comparison results window opens.
- Columns show the policy type, the setting, the baseline value and the effective state.
- The effective state is the real current configuration of DC01.

### 🔹 98. Understanding the Result Columns

<p align="center"><img src="images2/screenshot98.png" alt="Understanding the Result Columns" width="85%"/></p>

- The Baseline column shows the Microsoft recommended value.
- The Effective state column shows what is currently configured.
- Differences between them mean the machine is not following the baseline.

### 🔹 99. Open the View Menu

<p align="center"><img src="images2/screenshot99.png" alt="Open the View Menu" width="85%"/></p>

- The View menu at the top of Policy Analyzer is opened.
- It offers several filtering options for the long list of settings.
- Filtering helps focus on settings that need attention.

### 🔹 100. Show Only Differences

<p align="center"><img src="images2/screenshot100.png" alt="Show Only Differences" width="85%"/></p>

- Show only Differences is selected from the View menu.
- The list now contains only settings that differ from the baseline.
- This highlights every misconfiguration on the domain controller.

### 🔹 101. Misconfigurations Highlighted

<p align="center"><img src="images2/screenshot101.png" alt="Misconfigurations Highlighted" width="85%"/></p>

- Yellow cells show where the effective state does not match the baseline.
- Examples include audit policy settings that are set to No Auditing.
- These findings are documented as the top issues for the lab report.

### 🔹 102. Open the Local_Script Folder

<p align="center"><img src="images2/screenshot102.png" alt="Open the Local_Script Folder" width="85%"/></p>

- PowerShell is run as Administrator and the Local_Script folder of the baseline is opened with `cd`.
- This folder contains BaselineLocalInstall.ps1.
- The script applies the security baseline to the local machine.

### 🔹 103. Set the Execution Policy

<p align="center"><img src="images2/screenshot103.png" alt="Set the Execution Policy" width="85%"/></p>

- `Set-ExecutionPolicy RemoteSigned` is entered and confirmed with Y.
- This allows the downloaded baseline script to be executed.
- Without it, PowerShell would block the script.

### 🔹 104. Apply the Baseline on DC01

<p align="center"><img src="images2/screenshot104.png" alt="Apply the Baseline on DC01" width="85%"/></p>

- The command `.\BaselineLocalInstall.ps1 -WS2019DomainController` is executed.
- The script uses LGPO to import all recommended settings for a domain controller.
- The output shows each policy area being applied.

### 🔹 105. Open Local Group Policy Editor

<p align="center"><img src="images2/screenshot105.png" alt="Open Local Group Policy Editor" width="85%"/></p>

- `gpedit.msc` is run to open the Local Group Policy Editor.
- It is used to verify that the baseline has been applied.
- The Computer Configuration section is expanded.

### 🔹 106. Navigate to Account Policies

<p align="center"><img src="images2/screenshot106.png" alt="Navigate to Account Policies" width="85%"/></p>

- The path Computer Configuration > Windows Settings > Security Settings > Account Policies is opened.
- The Password Policy and Account Lockout Policy are listed.
- These are the first settings to review after applying the baseline.

### 🔹 107. Password Policy Verified

<p align="center"><img src="images2/screenshot107.png" alt="Password Policy Verified" width="85%"/></p>

- The Password Policy now shows stricter values than before.
- Examples are a longer minimum length and password history enforcement.
- This confirms the security baseline was applied successfully.

### 🔹 108. Baseline Applied on SRV01

<p align="center"><img src="images2/screenshot108.png" alt="Baseline Applied on SRV01" width="85%"/></p>

- The same baseline process is repeated on SRV01 using LGPO.
- The script output confirms that the settings were imported.
- Both DC01 and SRV01 are now aligned with Microsoft recommendations.

---

<a id="phase5"></a>

## 🔄 Phase 5 — Patch Management with BatchPatch

<sub>Steps 109–126</sub>

### 🔹 109. Run the BatchPatch Installer

<p align="center"><img src="images2/screenshot109.png" alt="Run the BatchPatch Installer" width="85%"/></p>

- The BatchPatch installer is launched on SRV01.
- A prompt asks the user to provide the location of PsExec.exe.
- PsExec is needed by BatchPatch to execute actions on remote machines.

### 🔹 110. Locate PsExec

<p align="center"><img src="images2/screenshot110.png" alt="Locate PsExec" width="85%"/></p>

- After clicking OK, a file browser opens.
- The PSTools folder is browsed to find PsExec.exe.
- BatchPatch stores this path for future remote operations.

### 🔹 111. Select PsExec.exe

<p align="center"><img src="images2/screenshot111.png" alt="Select PsExec.exe" width="85%"/></p>

- PsExec.exe is selected from the PSTools folder.
- BatchPatch confirms the file is valid.
- It will be used for remote command execution.

### 🔹 112. Register File Extensions

<p align="center"><img src="images2/screenshot112.png" alt="Register File Extensions" width="85%"/></p>

- BatchPatch asks if file extensions should be registered.
- Yes is selected.
- BatchPatch is now installed and opens its main grid.

### 🔹 113. Add Hosts to BatchPatch

<p align="center"><img src="images2/screenshot113.png" alt="Add Hosts to BatchPatch" width="85%"/></p>

- In the Grid menu, Add Hosts is selected.
- A window opens where machine names or IP addresses are typed.
- Each target machine is entered on its own line.

### 🔹 114. Hosts Added

<p align="center"><img src="images2/screenshot114.png" alt="Hosts Added" width="85%"/></p>

- The IP addresses of the lab machines are listed, including 192.168.10.102.
- After clicking OK, they appear as rows in the BatchPatch grid.
- These are the machines that will be scanned and patched.

### 🔹 115. Specify Alternate Credentials

<p align="center"><img src="images2/screenshot115.png" alt="Specify Alternate Credentials" width="85%"/></p>

- Actions > Specify alternate logon credentials is opened.
- This lets BatchPatch connect to the remote machines with an administrative account.
- The username is set to Administrator.

### 🔹 116. Enter Domain Credentials

<p align="center"><img src="images2/screenshot116.png" alt="Enter Domain Credentials" width="85%"/></p>

- The domain administrator password and the domain corp.local are entered.
- These credentials give BatchPatch the rights to query and install updates.
- Credentials are stored for the session only.

### 🔹 117. Credentials Saved

<p align="center"><img src="images2/screenshot117.png" alt="Credentials Saved" width="85%"/></p>

- Clicking OK stores the credentials.
- The grid is ready to connect to all hosts.
- The next step is scanning for missing updates.

### 🔹 118. Select All Hosts

<p align="center"><img src="images2/screenshot118.png" alt="Select All Hosts" width="85%"/></p>

- All hosts are selected in the grid with Ctrl+A.
- The Actions menu is opened and Windows Updates is chosen.
- Check for Available Updates is selected to start the scan.

### 🔹 119. Check for Available Updates

<p align="center"><img src="images2/screenshot119.png" alt="Check for Available Updates" width="85%"/></p>

- BatchPatch starts connecting to each machine.
- It queries Windows Update on every target.
- Status messages appear in the grid for each host.

### 🔹 120. Scan Results

<p align="center"><img src="images2/screenshot120.png" alt="Scan Results" width="85%"/></p>

- After a few minutes, the grid shows the number of missing updates per machine.
- This gives an overview of the patch level across the network.
- Machines with missing patches are selected for installation.

### 🔹 121. Select Machines for Patching

<p align="center"><img src="images2/screenshot121.png" alt="Select Machines for Patching" width="85%"/></p>

- The machines that show missing updates are highlighted.
- Actions > Windows Update > Download and Install is selected.
- This tells BatchPatch to push the patches remotely.

### 🔹 122. Reboot Option

<p align="center"><img src="images2/screenshot122.png" alt="Reboot Option" width="85%"/></p>

- The option Automatically reboot if required is checked.
- This makes sure updates that require a restart are completed.
- It reduces manual work after patching.

### 🔹 123. Confirm Installation

<p align="center"><img src="images2/screenshot123.png" alt="Confirm Installation" width="85%"/></p>

- OK is clicked to start downloading and installing updates.
- BatchPatch begins the remote installation on every selected host.
- Progress appears live in the grid.

### 🔹 124. Patching in Progress

<p align="center"><img src="images2/screenshot124.png" alt="Patching in Progress" width="85%"/></p>

- The grid shows real-time status such as downloading, installing and rebooting.
- Each machine has its own status line.
- This allows the administrator to track patching centrally.

### 🔹 125. Open Task Scheduler

<p align="center"><img src="images2/screenshot125.png" alt="Open Task Scheduler" width="85%"/></p>

- Actions > Task Scheduler is selected to automate patching.
- A scheduling window opens.
- Automatic schedules keep systems updated without manual effort.

### 🔹 126. Weekly Patch Schedule

<p align="center"><img src="images2/screenshot126.png" alt="Weekly Patch Schedule" width="85%"/></p>

- The recurrence is set to Weekly and a day and time are chosen.
- Clicking OK creates the scheduled patching task.
- Patches will now be installed automatically every week.

---

<a id="phase6"></a>

## 🛡️ Phase 6 — Windows Hardening

<sub>Steps 127–137</sub>

### 🔹 127. Open Local Security Policy

<p align="center"><img src="images2/screenshot127.png" alt="Open Local Security Policy" width="85%"/></p>

- On DC01, `secpol.msc` is started from PowerShell or the Run box.
- The Local Security Policy console opens.
- It is used to harden the authentication settings.

### 🔹 128. Open Security Options

<p align="center"><img src="images2/screenshot128.png" alt="Open Security Options" width="85%"/></p>

- Security Settings > Local Policies > Security Options is expanded.
- A long list of security-related policies is displayed.
- The NTLM-related setting is located in this list.

### 🔹 129. LAN Manager Authentication Level

<p align="center"><img src="images2/screenshot129.png" alt="LAN Manager Authentication Level" width="85%"/></p>

- Network security: LAN Manager authentication level is located.
- Double-clicking it opens its properties dialog.
- The setting controls which challenge-response protocol is allowed.

### 🔹 130. Send NTLMv2 Only

<p align="center"><img src="images2/screenshot130.png" alt="Send NTLMv2 Only" width="85%"/></p>

- The value is set to Send NTLMv2 response only. Refuse LM & NTLM.
- Older and weaker protocols are disabled.
- The same configuration is then repeated on SRV01.

### 🔹 131. Check Firewall Profiles

<p align="center"><img src="images2/screenshot131.png" alt="Check Firewall Profiles" width="85%"/></p>

- The command `Get-NetFirewallProfile | Select Name, Enabled` is run.
- It lists the Domain, Private and Public profiles with their status.
- All profiles should be enabled for proper protection.

### 🔹 132. Allow SMB from the Internal Network

<p align="center"><img src="images2/screenshot132.png" alt="Allow SMB from the Internal Network" width="85%"/></p>

- A firewall rule is created that allows SMB only from 192.168.10.0/24.
- This limits file sharing access to trusted lab machines.
- The command is run on SRV01 to protect the file share.

### 🔹 133. Block SMB from Everywhere Else

<p align="center"><img src="images2/screenshot133.png" alt="Block SMB from Everywhere Else" width="85%"/></p>

- A second rule named Block SMB External blocks traffic from 0.0.0.0/0.
- Together with the allow rule, only the internal subnet can reach SMB.
- This reduces exposure to attacks from outside the network.

### 🔹 134. Enable Advanced Audit Policies

<p align="center"><img src="images2/screenshot134.png" alt="Enable Advanced Audit Policies" width="85%"/></p>

- The `auditpol` command enables success and failure auditing for categories like DS Access.
- Auditing records important security events in the Windows Security log.
- These logs will later be collected by Wazuh.

### 🔹 135. More Audit Categories Enabled

<p align="center"><img src="images2/screenshot135.png" alt="More Audit Categories Enabled" width="85%"/></p>

- Additional `auditpol` commands are executed for other categories.
- Each command returns a success message.
- This increases visibility into logons, account changes and policy changes.

### 🔹 136. Verify Audit Policy

<p align="center"><img src="images2/screenshot136.png" alt="Verify Audit Policy" width="85%"/></p>

- `auditpol /get /category:*` is executed.
- The output lists every category and its configured setting.
- It confirms that success and failure auditing are active.

### 🔹 137. Audit Policy on SRV01

<p align="center"><img src="images2/screenshot137.png" alt="Audit Policy on SRV01" width="85%"/></p>

- The same audit commands are run on SRV01.
- Verification shows the settings are in place.
- Both servers now generate detailed security logs.

---

<a id="phase7"></a>

## 📡 Phase 7 — Wazuh SIEM Deployment & Agents

<sub>Steps 138–164</sub>

### 🔹 138. Wazuh Server Installation Begins

<p align="center"><img src="images2/screenshot138.jpeg" alt="Wazuh Server Installation Begins" width="85%"/></p>

- The Wazuh logo appears as the introduction to the Wazuh installation on IDS01.
- Wazuh is an open-source SIEM and XDR platform.
- It will collect, analyze and alert on security events across the lab.

### 🔹 139. Update the System

<p align="center"><img src="images2/screenshot139.jpeg" alt="Update the System" width="85%"/></p>

- The commands `sudo apt update && sudo apt upgrade -y` are run on IDS01.
- This makes sure all system packages are current before installing Wazuh.
- It also reduces dependency problems during installation.

### 🔹 140. Install curl

<p align="center"><img src="images2/screenshot140.jpeg" alt="Install curl" width="85%"/></p>

- The command `sudo apt install curl` is executed.
- curl is required to download the Wazuh installation script.
- The terminal shows the package being installed.

### 🔹 141. Run the Wazuh Install Script

<p align="center"><img src="images2/screenshot141.jpeg" alt="Run the Wazuh Install Script" width="85%"/></p>

- The curl command downloads wazuh-install.sh and runs it with the `-a` option.
- The terminal shows the indexer, manager, Filebeat and dashboard being installed step by step.
- The all-in-one installation sets up every Wazuh component on one machine.

### 🔹 142. Wazuh Admin Credentials

<p align="center"><img src="images2/screenshot142.jpeg" alt="Wazuh Admin Credentials" width="85%"/></p>

- After installation, the terminal prints the admin username and password.
- These credentials must be saved before closing the terminal.
- They are used to log in to the dashboard.

### 🔹 143. Open the Dashboard in the Browser

<p align="center"><img src="images2/screenshot143.jpeg" alt="Open the Dashboard in the Browser" width="85%"/></p>

- The server IP address is entered in the browser using https.
- A certificate warning appears because the certificate is self-signed.
- Click Advanced and proceed to continue.

### 🔹 144. Wazuh Login Page

<p align="center"><img src="images2/screenshot144.png" alt="Wazuh Login Page" width="85%"/></p>

- The Wazuh login page is displayed.
- The admin username and the saved password are entered.
- Successful login opens the main dashboard.

### 🔹 145. Wazuh Dashboard Overview

<p align="center"><img src="images2/screenshot145.jpeg" alt="Wazuh Dashboard Overview" width="85%"/></p>

- The dashboard shows the main modules of Wazuh.
- These include security events, integrity monitoring and vulnerability detection.
- Administrators use this interface to monitor all connected agents.

### 🔹 146. Open https://192.168.10.60

<p align="center"><img src="images2/screenshot146.png" alt="Open https://192.168.10.60" width="85%"/></p>

- The IDS01 address is opened in the browser on a lab machine.
- A security warning appears for the self-signed certificate.
- The warning is accepted to proceed to the login page.

### 🔹 147. Accept the Certificate Warning

<p align="center"><img src="images2/screenshot147.png" alt="Accept the Certificate Warning" width="85%"/></p>

- Advanced is expanded and the option to continue to the site is chosen.
- This is safe because the server is part of the controlled lab.
- The Wazuh login page then loads.

### 🔹 148. Logged In as admin

<p align="center"><img src="images2/screenshot148.png" alt="Logged In as admin" width="85%"/></p>

- The admin account is used to sign in to Wazuh.
- The main dashboard loads successfully.
- The SIEM is ready to receive data from agents.

### 🔹 149. Connect Agents to the Server

<p align="center"><img src="images2/screenshot149.png" alt="Connect Agents to the Server" width="85%"/></p>

- The section about connecting Wazuh agents is opened.
- A Wazuh agent must be installed on each endpoint.
- The first agent will be installed on a Windows VM.

### 🔹 150. Search for Wazuh Agent

<p align="center"><img src="images2/screenshot150.jpeg" alt="Search for Wazuh Agent" width="85%"/></p>

- The browser is used to search for the Wazuh agent download.
- The official Wazuh documentation website is chosen.
- Using the official site ensures the installer is genuine.

### 🔹 151. Open the Wazuh Website

<p align="center"><img src="images2/screenshot151.jpeg" alt="Open the Wazuh Website" width="85%"/></p>

- The official Wazuh installation guide is open.
- The GUI option and the Windows installer are selected.
- This leads to the MSI download for Windows.

### 🔹 152. Download the Windows Installer

<p align="center"><img src="images2/screenshot152.png" alt="Download the Windows Installer" width="85%"/></p>

- The Windows installer is selected on the website.
- The download begins automatically.
- The MSI file is saved in the Downloads folder.

### 🔹 153. Run the Agent Installer

<p align="center"><img src="images2/screenshot153.jpeg" alt="Run the Agent Installer" width="85%"/></p>

- The downloaded installer is started on the Windows VM.
- The setup wizard opens.
- Administrator rights are required to install it.

### 🔹 154. Install the Agent

<p align="center"><img src="images2/screenshot154.png" alt="Install the Agent" width="85%"/></p>

- Next is clicked through the setup wizard and the agent is installed.
- The wizard confirms the installation is finished.
- The agent manager can then be opened.

### 🔹 155. Open Wazuh Agent Manager

<p align="center"><img src="images2/screenshot155.png" alt="Open Wazuh Agent Manager" width="85%"/></p>

- The Wazuh Agent Manager window is opened after installation.
- The manager IP field must be filled in.
- It should contain the IP address of the Wazuh server, 192.168.10.60.

### 🔹 156. Enter the Manager IP

<p align="center"><img src="images2/screenshot156.jpeg" alt="Enter the Manager IP" width="85%"/></p>

- The Wazuh server IP is typed into the Manager IP field.
- Save is clicked to store the configuration.
- This tells the agent where to send its logs.

### 🔹 157. Start the Agent Service

<p align="center"><img src="images2/screenshot157.jpeg" alt="Start the Agent Service" width="85%"/></p>

- Start, Restart and Refresh are clicked in the Agent Manager.
- An authentication key is generated automatically.
- The status changes to Running.

### 🔹 158. Agent Registered

<p align="center"><img src="images2/screenshot158.jpeg" alt="Agent Registered" width="85%"/></p>

- The agent status shows Running and connected.
- The agent now appears in the Wazuh dashboard.
- The same steps are repeated on all other Windows machines.

### 🔹 159. Add the Wazuh GPG Key

<p align="center"><img src="images2/screenshot159.png" alt="Add the Wazuh GPG Key" width="85%"/></p>

- On the Linux machine, the Wazuh GPG key is downloaded and stored in the keyrings folder.
- This lets apt verify that packages come from Wazuh.
- It is the first step of adding the repository.

### 🔹 160. Add the Wazuh Repository

<p align="center"><img src="images2/screenshot160.png" alt="Add the Wazuh Repository" width="85%"/></p>

- The Wazuh apt repository line is added to the sources list.
- The command points apt to the official Wazuh package server.
- After this, `apt-get update` refreshes the package list.

### 🔹 161. Install the Wazuh Agent

<p align="center"><img src="images3/screenshot161.png" alt="Install the Wazuh Agent" width="85%"/></p>

- `sudo apt-get install wazuh-agent -y` is run on the Linux machine.
- The package manager downloads and installs the agent.
- Installation completes and the service is ready to configure.

### 🔹 162. Configure the Manager IP

<p align="center"><img src="images3/screenshot162.png" alt="Configure the Manager IP" width="85%"/></p>

- `sed` is used to replace MANAGER_IP in ossec.conf with 192.168.10.60.
- This points the Linux agent to the IDS01 Wazuh server.
- The agent configuration file is updated directly from the command line.

### 🔹 163. Start and Enable the Agent

<p align="center"><img src="images3/screenshot163.png" alt="Start and Enable the Agent" width="85%"/></p>

- `sudo systemctl start wazuh-agent` and `enable wazuh-agent` are executed.
- This starts the agent and configures it to launch at boot.
- The service status can then be checked with `systemctl status`.

### 🔹 164. Active Agent in Dashboard

<p align="center"><img src="images3/screenshot164.png" alt="Active Agent in Dashboard" width="85%"/></p>

- The Wazuh dashboard Agents page shows the Linux machine as Active.
- This confirms that logs are now flowing into Wazuh.
- Both Windows and Linux endpoints are now monitored.

---

<a id="phase8"></a>

## 🔍 Phase 8 — Sysmon & Log Collection

<sub>Steps 165–182</sub>

### 🔹 165. Download Sysmon

<p align="center"><img src="images3/screenshot165.png" alt="Download Sysmon" width="85%"/></p>

- The Sysinternals Sysmon download page is opened.
- Sysmon provides detailed Windows event logging beyond the default logs.
- The ZIP package is downloaded to the Windows VM.

### 🔹 166. Extract the Sysmon Package

<p align="center"><img src="images3/screenshot166.png" alt="Extract the Sysmon Package" width="85%"/></p>

- The downloaded Sysmon ZIP file is extracted.
- The extracted folder contains the Sysmon executables and license file.
- These files are used to install the Sysmon service.

### 🔹 167. Sysmon Package Contents

<p align="center"><img src="images3/screenshot167.png" alt="Sysmon Package Contents" width="85%"/></p>

- The extracted folder shows Eula, Sysmon, Sysmon64 and Sysmon64a files.
- Sysmon64 is the 64-bit executable used on modern Windows systems.
- A configuration file will be added to this same folder.

### 🔹 168. Download the SwiftOnSecurity Config

<p align="center"><img src="images3/screenshot168.jpeg" alt="Download the SwiftOnSecurity Config" width="85%"/></p>

- The SwiftOnSecurity Sysmon configuration is downloaded from GitHub.
- This is a widely used community configuration with strong default rules.
- It defines which events Sysmon should log in detail.

### 🔹 169. Copy the Config File

<p align="center"><img src="images3/screenshot169.png" alt="Copy the Config File" width="85%"/></p>

- The sysmonconfig-export file is copied into the same folder as sysmon64.exe.
- Keeping them together simplifies the installation command.
- The file will be renamed with an .xml extension.

### 🔹 170. Save Config as XML

<p align="center"><img src="images3/screenshot170.png" alt="Save Config as XML" width="85%"/></p>

- The configuration file is saved with the .xml extension in the Sysmon folder.
- This matches the format Sysmon expects when installing.
- The next step installs Sysmon using this configuration.

### 🔹 171. Install Sysmon with the Config

<p align="center"><img src="images3/screenshot171.png" alt="Install Sysmon with the Config" width="85%"/></p>

- Command Prompt is run as Administrator and `sysmon64.exe -i sysmonconfig-export.xml` is executed.
- This installs Sysmon as a Windows service using the custom configuration.
- A license agreement prompt appears first.

### 🔹 172. Accept the License Agreement

<p align="center"><img src="images3/screenshot172.png" alt="Accept the License Agreement" width="85%"/></p>

- The Sysmon license prompt is accepted by clicking Agree.
- Installation then proceeds in the terminal.
- A success message appears once Sysmon is installed.

### 🔹 173. Installation Successful

<p align="center"><img src="images3/screenshot173.jpeg" alt="Installation Successful" width="85%"/></p>

- The terminal confirms that Sysmon was installed successfully.
- The Sysmon service is now running and logging system activity.
- Its events can be viewed in Event Viewer.

### 🔹 174. View Sysmon Logs in Event Viewer

<p align="center"><img src="images3/screenshot174.png" alt="View Sysmon Logs in Event Viewer" width="85%"/></p>

- Event Viewer is expanded to Microsoft > Windows > Sysmon > Operational.
- Detailed process, network and file events generated by Sysmon are listed here.
- These logs will next be forwarded into Wazuh.

### 🔹 175. Locate the Agent Config File

<p align="center"><img src="images3/screenshot175.jpeg" alt="Locate the Agent Config File" width="85%"/></p>

- The Wazuh agent configuration file ossec.conf is located under `Program Files (x86)\ossec-agent`.
- View Config is used to open it for editing.
- Log sources are defined inside this file.

### 🔹 176. Add Sysmon as a Log Source

<p align="center"><img src="images3/screenshot176.png" alt="Add Sysmon as a Log Source" width="85%"/></p>

- A localfile entry for the Sysmon Operational log is added inside the configuration.
- This tells the Wazuh agent to forward Sysmon events.
- The agent service must be restarted to apply the change.

### 🔹 177. Restart the Wazuh Agent

<p align="center"><img src="images3/screenshot177.jpeg" alt="Restart the Wazuh Agent" width="85%"/></p>

- The Wazuh agent service is restarted after editing the configuration.
- This loads the new Sysmon log source.
- Sysmon events should now start appearing in the dashboard.

### 🔹 178. Wazuh Rules for Sysmon Events

<p align="center"><img src="images3/screenshot178.jpeg" alt="Wazuh Rules for Sysmon Events" width="85%"/></p>

- The Wazuh dashboard now shows Sysmon-based rule hits such as FileCreate, ProcessAccess and RegistryEvent.
- Each entry includes a timestamp, rule description, level and rule ID.
- This confirms that Sysmon telemetry is being analyzed by Wazuh rules.

### 🔹 179. Sysmon Data Confirmed in Wazuh

<p align="center"><img src="images3/screenshot179.jpeg" alt="Sysmon Data Confirmed in Wazuh" width="85%"/></p>

- Further browsing of the dashboard shows ongoing Sysmon events being logged.
- Process creation and image load events appear alongside agent status messages.
- The endpoint is now fully integrated with the SIEM.

### 🔹 180. Open Agent Log Configuration

<p align="center"><img src="images3/screenshot180.png" alt="Open Agent Log Configuration" width="85%"/></p>

- On another Wazuh agent, View Config is opened to check the localfile section.
- The entries required for log collection are reviewed.
- Any missing log sources are added here.

### 🔹 181. Verify Localfile Entries

<p align="center"><img src="images3/screenshot181.png" alt="Verify Localfile Entries" width="85%"/></p>

- The closing localfile tag confirms the log source block is complete.
- The configuration file is saved after the edit.
- The Wazuh service is restarted to apply the new settings.

### 🔹 182. Restart the Wazuh Service

<p align="center"><img src="images3/screenshot182.png" alt="Restart the Wazuh Service" width="85%"/></p>

- The Wazuh agent service is restarted to load the updated log collection settings.
- This ensures the newly added log sources start being monitored.
- Log collection is now active across all configured agents.

---

<a id="phase9"></a>

## 🎯 Phase 9 — Custom Detection Rules & MITRE ATT&CK

<sub>Steps 183–189</sub>

### 🔹 183. Open local_rules.xml

<p align="center"><img src="images3/screenshot183.png" alt="Open local_rules.xml" width="85%"/></p>

- On IDS01, `sudo nano /var/ossec/etc/rules/local_rules.xml` is run.
- This file is where custom detection rules are defined.
- It currently needs the lab's custom rule set added.

### 🔹 184. Add Custom Detection Rules

<p align="center"><img src="images3/screenshot184.png" alt="Add Custom Detection Rules" width="85%"/></p>

- The existing content is cleared and new custom rules are pasted in.
- These rules detect the specific attack techniques used later in the lab.
- The file is saved with Ctrl+S and closed with Ctrl+X.

### 🔹 185. Restart the Wazuh Manager

<p align="center"><img src="images3/screenshot185.png" alt="Restart the Wazuh Manager" width="85%"/></p>

- `sudo systemctl restart wazuh-manager` is executed to load the new rules.
- A restart is required for custom rules to take effect.
- The manager comes back online with the updated rule set.

### 🔹 186. Verify Rules Loaded Without Errors

<p align="center"><img src="images3/screenshot186.png" alt="Verify Rules Loaded Without Errors" width="85%"/></p>

- The Wazuh log is checked to confirm the manager started cleanly.
- No rule syntax errors are reported.
- The custom detection rules are now active.

### 🔹 187. Open Wazuh Dashboard for MITRE

<p align="center"><img src="images3/screenshot187.png" alt="Open Wazuh Dashboard for MITRE" width="85%"/></p>

- The Wazuh dashboard is opened at https://192.168.10.60.
- The MITRE ATT&CK module is accessed from the left-hand menu.
- It maps detected events to known adversary techniques.

### 🔹 188. Open the MITRE ATT&CK Menu

<p align="center"><img src="images3/screenshot188.png" alt="Open the MITRE ATT&CK Menu" width="85%"/></p>

- MITRE ATT&CK is selected from the left navigation menu.
- The module loads the ATT&CK matrix inside the dashboard.
- Techniques will light up as matching events are detected.

### 🔹 189. ATT&CK Matrix View

<p align="center"><img src="images3/screenshot189.png" alt="ATT&CK Matrix View" width="85%"/></p>

- The ATT&CK matrix is displayed with tactics across the top and techniques below.
- No techniques are highlighted yet because no attacks have been run.
- This view will be revisited after the attack simulations.

---

<a id="phase10"></a>

## ⚔️ Phase 10 — Attack Simulation from Kali

<sub>Steps 190–214</sub>

### 🔹 190. Update Attacker Tools

<p align="center"><img src="images3/screenshot190.png" alt="Update Attacker Tools" width="85%"/></p>

- On the Kali attacker machine, `apt-get update` and `upgrade` are run.
- This keeps the operating system and security tools current.
- Up-to-date tools behave more reliably during the simulated attacks.

### 🔹 191. Install CrackMapExec

<p align="center"><img src="images3/screenshot191.png" alt="Install CrackMapExec" width="85%"/></p>

- `sudo apt-get install crackmapexec -y` installs the SMB attack tool.
- CrackMapExec is used later for enumeration and credential attacks.
- The terminal confirms the package installation.

### 🔹 192. Install Hydra

<p align="center"><img src="images3/screenshot192.png" alt="Install Hydra" width="85%"/></p>

- `sudo apt-get install hydra -y` installs the brute-force tool Hydra.
- Hydra will be used to simulate password-guessing attacks.
- Its installation output is shown in the terminal.

### 🔹 193. Install python3-impacket

<p align="center"><img src="images3/screenshot193.png" alt="Install python3-impacket" width="85%"/></p>

- `sudo apt-get install python3-impacket -y` is run.
- Impacket provides additional SMB and network protocol tools.
- It supports some of the enumeration techniques used later.

### 🔹 194. Verify Tool Versions

<p align="center"><img src="images3/screenshot194.png" alt="Verify Tool Versions" width="85%"/></p>

- `cme --version` is run to confirm CrackMapExec is installed correctly.
- The version number is printed to the terminal.
- This confirms the tool is ready for use.

### 🔹 195. Verify Hydra Help Output

<p align="center"><img src="images3/screenshot195.png" alt="Verify Hydra Help Output" width="85%"/></p>

- `hydra -h | head -5` is run to display the start of Hydra's help text.
- This confirms Hydra was installed and runs correctly.
- The attacker machine is now fully equipped for Phase 10.

### 🔹 196. Network Reconnaissance Scan

<p align="center"><img src="images3/screenshot196.png" alt="Network Reconnaissance Scan" width="85%"/></p>

- `nmap -sn 192.168.10.0/24` is run from the attacker to discover live hosts.
- This is a ping sweep that lists every responding machine on the subnet.
- It corresponds to MITRE technique **T1046**, Network Service Discovery.

### 🔹 197. Detailed Scan of DC01

<p align="center"><img src="images3/screenshot197.png" alt="Detailed Scan of DC01" width="85%"/></p>

- `nmap -sV -sC -O 192.168.10.10` is run against the domain controller.
- This reveals open ports, running services, script results and OS guess.
- The output helps the attacker understand what services DC01 exposes.

### 🔹 198. Full Port Scan

<p align="center"><img src="images3/screenshot198.png" alt="Full Port Scan" width="85%"/></p>

- `nmap -A -p- 192.168.10.10 --max-retries 2` scans all 65535 ports on DC01.
- The -A flag adds OS detection, version detection and script scanning.
- This is a much more thorough scan than the earlier quick check.

### 🔹 199. SMB OS Discovery on SRV01

<p align="center"><img src="images3/screenshot199.png" alt="SMB OS Discovery on SRV01" width="85%"/></p>

- `nmap -p 139,445 --script smb-os-discovery 192.168.10.25` targets the SMB ports on SRV01.
- The script reports OS version and domain information over SMB.
- This kind of probe is exactly what the Wazuh rules are designed to detect.

### 🔹 200. Check Wazuh for Scan Alerts

<p align="center"><img src="images3/screenshot200.png" alt="Check Wazuh for Scan Alerts" width="85%"/></p>

- The Wazuh dashboard Security Events are filtered by agent DC01.
- Alerts related to the nmap scans should appear here.
- This confirms the SIEM detected the reconnaissance activity.

### 🔹 201. SMB Share and User Enumeration

<p align="center"><img src="images3/screenshot201.png" alt="SMB Share and User Enumeration" width="85%"/></p>

- `nmap --script smb-enum-shares,smb-enum-users -p 445` is run against DC01.
- The script lists available SMB shares and domain user accounts.
- This technique corresponds to MITRE **T1021.002**, SMB/Windows Admin Shares.

### 🔹 202. Enumerate Shares on SRV01

<p align="center"><img src="images3/screenshot202.png" alt="Enumerate Shares on SRV01" width="85%"/></p>

- The same smb-enum-shares script is run against SRV01 at 192.168.10.25.
- It checks which shares are visible without authentication.
- The FinanceData share is expected to be hidden from anonymous enumeration.

### 🔹 203. CrackMapExec Network Sweep

<p align="center"><img src="images3/screenshot203.png" alt="CrackMapExec Network Sweep" width="85%"/></p>

- `crackmapexec smb 192.168.10.0/24` is run to identify all SMB hosts on the subnet.
- CrackMapExec reports the hostname, OS and domain for each responding machine.
- This gives the attacker a map of the Windows infrastructure.

### 🔹 204. Enumerate Shares with CrackMapExec

<p align="center"><img src="images3/screenshot204.png" alt="Enumerate Shares with CrackMapExec" width="85%"/></p>

- `crackmapexec smb 192.168.10.25 --shares` lists the shares exposed by SRV01.
- This checks whether FinanceData is visible without valid credentials.
- The access-based enumeration setting should limit what appears.

### 🔹 205. Enumerate Users with CrackMapExec

<p align="center"><img src="images3/screenshot205.png" alt="Enumerate Users with CrackMapExec" width="85%"/></p>

- `crackmapexec smb 192.168.10.10 --users` attempts to list domain users from DC01.
- This simulates an attacker trying to build a list of valid account names.
- Such enumeration attempts should also trigger Wazuh alerts.

### 🔹 206. Confirm FinanceData Is Protected

<p align="center"><img src="images3/screenshot206.png" alt="Confirm FinanceData Is Protected" width="85%"/></p>

- The enumeration results confirm the FinanceData share is not accessible anonymously.
- SMB enumeration attempts are logged by Sysmon and forwarded to Wazuh.
- This validates that the RBAC and auditing controls are working together.

### 🔹 207. Prepare the Password List

<p align="center"><img src="images3/screenshot207.png" alt="Prepare the Password List" width="85%"/></p>

- A password list file is created on the attacker machine using a heredoc (EOF).
- This wordlist will be used for the brute-force attack against martin's account.
- Using a weak, unauthorized account mirrors a realistic attack scenario.

### 🔹 208. Hydra Brute Force Against SMB

<p align="center"><img src="images3/screenshot208.png" alt="Hydra Brute Force Against SMB" width="85%"/></p>

- `hydra -l martin -P /tmp/passwords.txt smb://192.168.10.10` is run.
- Hydra tries each password in the list against the martin account over SMB.
- This corresponds to MITRE **T1110**, Brute Force.

### 🔹 209. CrackMapExec Brute Force

<p align="center"><img src="images3/screenshot209.png" alt="CrackMapExec Brute Force" width="85%"/></p>

- `crackmapexec smb 192.168.10.10 -u martin -p /tmp/passwords.txt` attempts the same attack with CrackMapExec.
- CrackMapExec reports which password attempts succeed or fail.
- Running two tools cross-validates the brute-force results.

### 🔹 210. Check Event Viewer for Failed Logons

<p align="center"><img src="images3/screenshot210.png" alt="Check Event Viewer for Failed Logons" width="85%"/></p>

- On DC01, Event Viewer > Windows Logs > Security is checked for Event ID 4625 and 4740.
- Event ID 4625 records failed logon attempts and 4740 records account lockouts.
- These events prove the brute-force attack was captured by Windows auditing.

### 🔹 211. Test Multiple Accounts with One Password

<p align="center"><img src="images3/screenshot211.png" alt="Test Multiple Accounts with One Password" width="85%"/></p>

- `crackmapexec smb 192.168.10.10 -u bob -u john -u martin -p Finance@123` tests several usernames against one password.
- This simulates a password-spraying style attack across known accounts.
- Results show which accounts, if any, accept the shared password.

### 🔹 212. Authenticated Share Check

<p align="center"><img src="images3/screenshot212.png" alt="Authenticated Share Check" width="85%"/></p>

- `crackmapexec smb 192.168.10.25 -u john -p Finance@123 --shares` uses john's valid credentials to list shares on SRV01.
- Because john is authorized, FinanceData should now appear in the results.
- This contrasts with the earlier anonymous enumeration attempt.

### 🔹 213. Attempt Administrator Credentials

<p align="center"><img src="images3/screenshot213.png" alt="Attempt Administrator Credentials" width="85%"/></p>

- `crackmapexec smb 192.168.10.10 -u Administrator -p Admin@SRV01 --shares` tries a guessed administrator password.
- This checks whether weak or reused admin credentials would expose the domain controller.
- A failed attempt shows the account is protected by a stronger password.

### 🔹 214. Check Logged-On Users

<p align="center"><img src="images3/screenshot214.png" alt="Check Logged-On Users" width="85%"/></p>

- `crackmapexec smb 192.168.10.10 -u Administrator -p YourAdminPass --loggedon-users` is run with valid administrator credentials.
- This lists which users are currently logged on to DC01.
- It demonstrates what an attacker could learn after obtaining valid credentials.

---

<a id="phase11"></a>

## 🚨 Phase 11 — Active Response (Detection → Prevention)

<sub>Steps 215–218</sub>

### 🔹 215. Open ossec.conf for Active Response

<p align="center"><img src="images3/screenshot215.png" alt="Open ossec.conf for Active Response" width="85%"/></p>

- `sudo nano /var/ossec/etc/ossec.conf` is opened on the Wazuh manager.
- The active-response section will be added to automatically react to attacks.
- This moves the lab from detection to automated prevention.

### 🔹 216. Add the Active Response Configuration

<p align="center"><img src="images3/screenshot216.png" alt="Add the Active Response Configuration" width="85%"/></p>

- The active-response block is inserted before the closing global tag.
- It defines which command runs and which rule levels trigger it.
- Typically this blocks the attacking IP address automatically.

### 🔹 217. Restart the Manager to Apply Active Response

<p align="center"><img src="images3/screenshot217.png" alt="Restart the Manager to Apply Active Response" width="85%"/></p>

- `sudo systemctl restart wazuh-manager` reloads the configuration.
- The active response module is now enabled.
- Future high-severity alerts, such as repeated brute-force attempts, will trigger an automatic block.

### 🔹 218. Verify Active Response Is Configured

<p align="center"><img src="images3/screenshot218.png" alt="Verify Active Response Is Configured" width="85%"/></p>

- `sudo grep -A5 active-response /var/ossec/etc/ossec.conf` displays the saved configuration block.
- This confirms the settings were written correctly to the file.
- With active response enabled, the Module 5 lab setup is complete.

---

---

## 🎯 Key Outcomes

| ✅ Achievement | 📝 Result |
| :--- | :--- |
| **RBAC enforced** | `john` (FinanceData member) gets access; `martin` is denied |
| **Defense in depth** | Share + NTFS permissions, access-based enumeration, SMB encryption |
| **Baseline hardening** | Microsoft baseline applied; NTLMv2-only; SMB restricted to `192.168.10.0/24` |
| **Visibility** | Advanced auditing + Sysmon forwarded into Wazuh |
| **Detection** | Custom rules + MITRE ATT&CK mapping (T1046, T1021.002, T1110) |
| **Prevention** | Wazuh Active Response blocks attacking IPs automatically |

---

<div align="center">

## 📬 Connect With Me

[![Email](https://img.shields.io/badge/EMAIL-sanawarimran.pk@gmail.com-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:sanawarimran.pk@gmail.com)
[![LinkedIn](https://img.shields.io/badge/LINKEDIN-Sanawar%20Imran-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/sanawarimran)
[![GitHub](https://img.shields.io/badge/GITHUB-sanawar--imran--securityengineer-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/sanawar-imran-securityengineer/)
[![Medium](https://img.shields.io/badge/MEDIUM-@sanawarimran.pk-000000?style=for-the-badge&logo=medium&logoColor=white)](https://medium.com/@sanawarimran.pk)

💬 *Questions? Need lab files? Feel free to reach out!*

---

## 📄 License

This project is created for **educational purposes** and is open-source.

---

### ⭐ If you found this helpful, please give it a star!

Made with 💙 by **Sanawar Imran**
*BS Cybersecurity • Air University, Multan Campus*

*Last Updated: November 2026*

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:2c5364,100:0f2027&height=100&section=footer" width="100%"/>

</div>
