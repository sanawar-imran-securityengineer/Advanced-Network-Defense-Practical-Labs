<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0f2027,50:203a43,100:2c5364&height=220&section=header&text=Endpoint%20Security&fontSize=58&fontColor=ffffff&animation=fadeIn&fontAlignY=36&desc=Windows%20Systems%20%E2%80%A2%20AD%20RBAC%20%E2%80%A2%20Wazuh%20SIEM%20%E2%80%A2%20MITRE%20ATT%26CK&descSize=18&descAlignY=58" width="100%"/>

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
| 1 | 🖥️ Phase 1 — VM Setup & Domain Join | 1–20 |
| 2 | 🏢 Phase 2 — Active Directory & RBAC Design | 21–42 |
| 3 | 📁 Phase 3 — Secure File Sharing (FinanceData) | 43–80 |
| 4 | 📏 Phase 4 — Microsoft Security Compliance Toolkit | 81–108 |
| 5 | 🔄 Phase 5 — Patch Management with BatchPatch | 109–126 |
| 6 | 🛡️ Phase 6 — Windows Hardening | 127–137 |
| 7 | 📡 Phase 7 — Wazuh SIEM Deployment & Agents | 138–164 |
| 8 | 🔍 Phase 8 — Sysmon & Log Collection | 165–182 |
| 9 | 🎯 Phase 9 — Custom Detection Rules & MITRE ATT&CK | 183–189 |
| 10 | ⚔️ Phase 10 — Attack Simulation from Kali | 190–214 |
| 11 | 🚨 Phase 11 — Active Response (Detection → Prevention) | 215–218 |

---

## 🧪 Lab Walkthrough

> 👇 Click a phase to expand it.

<details>
<summary><b>🖥️ Phase 1 — VM Setup & Domain Join</b> &nbsp;·&nbsp; Steps 1–20</summary>

<br>

#### 🔹 1. SRV01 Virtual Machine Ready

<p align="center"><img src="images/ai.png" alt="SRV01 Virtual Machine Ready" width="80%"/></p>

> Windows Server 2019 VM in VMware (2 GB RAM, 2 CPU, 40 GB NVMe, VMnet1). Acts as Patch Management + Secure File Server.

#### 🔹 2. Open Network & Internet Settings

<p align="center"><img src="images/screenshot2.png" alt="Open Network & Internet Settings" width="80%"/></p>

> Right-click the network icon on SRV01 to start assigning a fixed IP.

#### 🔹 3. Change Adapter Options

<p align="center"><img src="images/screenshot3.png" alt="Change Adapter Options" width="80%"/></p>

> Open Network Connections and pick the correct Ethernet adapter.

#### 🔹 4. Ethernet Adapter Properties

<p align="center"><img src="images/screenshot4.png" alt="Ethernet Adapter Properties" width="80%"/></p>

> Open adapter Properties to reach the TCP/IPv4 protocol.

#### 🔹 5. Select IPv4 Properties

<p align="center"><img src="images/screenshot5.png" alt="Select IPv4 Properties" width="80%"/></p>

> Switch from DHCP to "Use the following IP address".

#### 🔹 6. Static IP Configuration for SRV01

<p align="center"><img src="images/screenshot6.png" alt="Static IP Configuration for SRV01" width="80%"/></p>

> IP `192.168.10.25`, mask `255.255.255.0`, gateway `192.168.10.1`, DNS `192.168.10.10` (DC01) — required to join `corp.local`.

#### 🔹 7. Verify IP with ipconfig /all

<p align="center"><img src="images/screenshot7.png" alt="Verify IP with ipconfig /all" width="80%"/></p>

> Confirms the static address is applied on SRV01.

#### 🔹 8. Ping Test to DC01

<p align="center"><img src="images/screenshot8.png" alt="Ping Test to DC01" width="80%"/></p>

> `ping 192.168.10.10` succeeds — connectivity to the domain controller verified.

#### 🔹 9. Open System Information

<p align="center"><img src="images/screenshot9.png" alt="Open System Information" width="80%"/></p>

> Settings → System → About to rename the host to SRV01.

#### 🔹 10. Advanced System Settings

<p align="center"><img src="images/screenshot10.png" alt="Advanced System Settings" width="80%"/></p>

> Computer Name tab → Change to rename and join the domain.

#### 🔹 11. Computer Name / Domain Changes

<p align="center"><img src="images/screenshot11.png" alt="Computer Name / Domain Changes" width="80%"/></p>

> Computer name set to SRV01 before switching to domain membership.

#### 🔹 12. Join corp.local Domain

<p align="center"><img src="images/screenshot12.png" alt="Join corp.local Domain" width="80%"/></p>

> Domain admin credentials used; VM restarted to apply name + domain.

#### 🔹 13. Clone CL01 to Create CL02

<p align="center"><img src="images/screenshot13.png" alt="Clone CL01 to Create CL02" width="80%"/></p>

> VMware Manage → Clone to create the second client (unauthorized workstation for RBAC tests).

#### 🔹 14. Create a Full Clone

<p align="center"><img src="images/screenshot14.png" alt="Create a Full Clone" width="80%"/></p>

> Full clone chosen so CL02 is independent of CL01.

#### 🔹 15. Name the Clone CL02

<p align="center"><img src="images/screenshot15.png" alt="Name the Clone CL02" width="80%"/></p>

> Clone named CL02 and added to the VMware library.

#### 🔹 16. Static IP Configuration for CL02

<p align="center"><img src="images/screenshot16.png" alt="Static IP Configuration for CL02" width="80%"/></p>

> Unique IP in `192.168.10.x`, DNS `192.168.10.10` to avoid conflicts.

#### 🔹 17. Connectivity Test from CL02

<p align="center"><img src="images/screenshot17.png" alt="Connectivity Test from CL02" width="80%"/></p>

> Ping to DC01 succeeds — network verified before domain join.

#### 🔹 18. Open Advanced System Settings on CL02

<p align="center"><img src="images/screenshot18.png" alt="Open Advanced System Settings on CL02" width="80%"/></p>

> System Properties → Change to rename and join the domain.

#### 🔹 19. Join CL02 to corp.local

<p align="center"><img src="images/screenshot19.png" alt="Join CL02 to corp.local" width="80%"/></p>

> Welcome message confirms CL02 joined the domain.

#### 🔹 20. Login as CORP\Administrator

<p align="center"><img src="images/screenshot20.png" alt="Login as CORP\Administrator" width="80%"/></p>

> Domain authentication verified on CL02.

</details>

<details>
<summary><b>🏢 Phase 2 — Active Directory & RBAC Design</b> &nbsp;·&nbsp; Steps 21–42</summary>

<br>

#### 🔹 21. Open Active Directory Users and Computers

<p align="center"><img src="images/screenshot21.png" alt="Open Active Directory Users and Computers" width="80%"/></p>

> On DC01 run `dsa.msc` — all OUs, users and groups are created here.

#### 🔹 22. Create the Finance OU

<p align="center"><img src="images/screenshot22.png" alt="Create the Finance OU" width="80%"/></p>

> New → Organizational Unit named **Finance**.

#### 🔹 23. Finance OU Created

<p align="center"><img src="images/screenshot23.png" alt="Finance OU Created" width="80%"/></p>

> OU appears under `corp.local` for per-department management.

#### 🔹 24. Create the FinanceUsers Sub-OU

<p align="center"><img src="images/screenshot24.png" alt="Create the FinanceUsers Sub-OU" width="80%"/></p>

> Holds authorized finance accounts (bob, john).

#### 🔹 25. OU Structure Verified

<p align="center"><img src="images/screenshot25.png" alt="OU Structure Verified" width="80%"/></p>

> Finance → FinanceUsers hierarchy matches the design.

#### 🔹 26. Create User bob

<p align="center"><img src="images/screenshot26.png" alt="Create User bob" width="80%"/></p>

> Authorized finance user in FinanceUsers OU.

#### 🔹 27. Set bob's Password

<p align="center"><img src="images/screenshot27.png" alt="Set bob's Password" width="80%"/></p>

> "Password never expires" enabled for stable lab testing.

#### 🔹 28. bob Account Created

<p align="center"><img src="images/screenshot28.png" alt="bob Account Created" width="80%"/></p>

> Account summary confirmed; ready for FinanceData group.

#### 🔹 29. Create User john

<p align="center"><img src="images/screenshot29.png" alt="Create User john" width="80%"/></p>

> Second authorized finance user.

#### 🔹 30. Set john's Password

<p align="center"><img src="images/screenshot30.png" alt="Set john's Password" width="80%"/></p>

> Same password options as bob.

#### 🔹 31. Create User martin (Unauthorized)

<p align="center"><img src="images/screenshot31.png" alt="Create User martin (Unauthorized)" width="80%"/></p>

> Deliberately **not** in FinanceData — represents the unauthorized user.

#### 🔹 32. Create User alice

<p align="center"><img src="images/screenshot32.png" alt="Create User alice" width="80%"/></p>

> General employee with no finance privileges.

#### 🔹 33. Set alice's Password

<p align="center"><img src="images/screenshot33.png" alt="Set alice's Password" width="80%"/></p>

> Consistent account options across all test users.

#### 🔹 34. alice Account Created

<p align="center"><img src="images/screenshot34.png" alt="alice Account Created" width="80%"/></p>

> All four lab users (bob, john, martin, alice) now exist.

#### 🔹 35. Create the FinanceData Security Group

<p align="center"><img src="images/screenshot35.png" alt="Create the FinanceData Security Group" width="80%"/></p>

> Global / Security group — permissions go to the group, not individuals (core of RBAC).

#### 🔹 36. FinanceData Group Created

<p align="center"><img src="images/screenshot36.png" alt="FinanceData Group Created" width="80%"/></p>

> Group appears in the Finance OU, currently empty.

#### 🔹 37. Open Group Members Tab

<p align="center"><img src="images/screenshot37.png" alt="Open Group Members Tab" width="80%"/></p>

> Add only authorized finance employees.

#### 🔹 38. Add bob to the Group

<p align="center"><img src="images/screenshot38.png" alt="Add bob to the Group" width="80%"/></p>

> Resolved via Check Names and added.

#### 🔹 39. Add john to the Group

<p align="center"><img src="images/screenshot39.png" alt="Add john to the Group" width="80%"/></p>

> Added the same way.

#### 🔹 40. Group Membership Confirmed

<p align="center"><img src="images/screenshot40.png" alt="Group Membership Confirmed" width="80%"/></p>

> Members: bob and john. martin and alice intentionally excluded.

#### 🔹 41. Move CL02 to the Workstations OU

<p align="center"><img src="images/screenshot41.png" alt="Move CL02 to the Workstations OU" width="80%"/></p>

> Allows workstation policies to apply to CL02.

#### 🔹 42. CL02 in Workstations OU

<p align="center"><img src="images/screenshot42.png" alt="CL02 in Workstations OU" width="80%"/></p>

> Move confirmed — identity structure is ready.

</details>

<details>
<summary><b>📁 Phase 3 — Secure File Sharing (FinanceData)</b> &nbsp;·&nbsp; Steps 43–80</summary>

<br>

#### 🔹 43. Open File Explorer on SRV01

<p align="center"><img src="images/screenshot43.png" alt="Open File Explorer on SRV01" width="80%"/></p>

> Start preparing the shared folder.

#### 🔹 44. Choose the Drive

<p align="center"><img src="images/screenshot44.png" alt="Choose the Drive" width="80%"/></p>

> Select the drive hosting the Shares folder.

#### 🔹 45. Create the Shares Folder

<p align="center"><img src="images/screenshot45.png" alt="Create the Shares Folder" width="80%"/></p>

> Parent folder for all controlled shares.

#### 🔹 46. Create the FinanceData Folder

<p align="center"><img src="images/screenshot46.png" alt="Create the FinanceData Folder" width="80%"/></p>

> Will hold confidential finance files.

#### 🔹 47. Final Folder Path

<p align="center"><img src="images/screenshot47.png" alt="Final Folder Path" width="80%"/></p>

> `C:\Shares\FinanceData`

#### 🔹 48. Open Folder Properties

<p align="center"><img src="images/screenshot48.png" alt="Open Folder Properties" width="80%"/></p>

> Sharing tab → Advanced Sharing.

#### 🔹 49. Advanced Sharing Dialog

<p align="center"><img src="images/screenshot49.png" alt="Advanced Sharing Dialog" width="80%"/></p>

> Folder is not yet shared until enabled.

#### 🔹 50. Enable Share This Folder

<p align="center"><img src="images/screenshot50.png" alt="Enable Share This Folder" width="80%"/></p>

> Share name set to **FinanceData**.

#### 🔹 51. Open Share Permissions

<p align="center"><img src="images/screenshot51.png" alt="Open Share Permissions" width="80%"/></p>

> Default `Everyone` access is too open for finance data.

#### 🔹 52. Remove Everyone

<p align="center"><img src="images/screenshot52.png" alt="Remove Everyone" width="80%"/></p>

> Enforces least privilege.

#### 🔹 53. Add the FinanceData Group

<p align="center"><img src="images/screenshot53.png" alt="Add the FinanceData Group" width="80%"/></p>

> Group resolved from Active Directory.

#### 🔹 54. Grant Full Control to the Group

<p align="center"><img src="images/screenshot54.png" alt="Grant Full Control to the Group" width="80%"/></p>

> Share-level permission; NTFS applies the stricter rules.

#### 🔹 55. Open Caching Settings

<p align="center"><img src="images/screenshot55.png" alt="Open Caching Settings" width="80%"/></p>

> Offline settings window.

#### 🔹 56. Caching Disabled and Applied

<p align="center"><img src="images/screenshot56.png" alt="Caching Disabled and Applied" width="80%"/></p>

> Prevents confidential files being cached on clients.

#### 🔹 57. Open the Security Tab

<p align="center"><img src="images/screenshot57.png" alt="Open the Security Tab" width="80%"/></p>

> View current NTFS permissions.

#### 🔹 58. Advanced Security Settings

<p align="center"><img src="images/screenshot58.png" alt="Advanced Security Settings" width="80%"/></p>

> Inheritance controls.

#### 🔹 59. Disable Inheritance

<p align="center"><img src="images/screenshot59.png" alt="Disable Inheritance" width="80%"/></p>

> Convert inherited permissions to explicit entries.

#### 🔹 60. Explicit Permissions Listed

<p align="center"><img src="images/screenshot60.png" alt="Explicit Permissions Listed" width="80%"/></p>

> Entries can now be edited individually.

#### 🔹 61. Remove Unnecessary Entries

<p align="center"><img src="images/screenshot61.png" alt="Remove Unnecessary Entries" width="80%"/></p>

> Only Administrators and SYSTEM remain.

#### 🔹 62. Add the FinanceData Group in NTFS

<p align="center"><img src="images/screenshot62.png" alt="Add the FinanceData Group in NTFS" width="80%"/></p>

> Select a principal → Check Names.

#### 🔹 63. Set NTFS Permissions for the Group

<p align="center"><img src="images/screenshot63.png" alt="Set NTFS Permissions for the Group" width="80%"/></p>

> Full Control on this folder, subfolders and files.

#### 🔹 64. Apply the NTFS Changes

<p align="center"><img src="images/screenshot64.png" alt="Apply the NTFS Changes" width="80%"/></p>

> Updated ACL saved.

#### 🔹 65. NTFS Permissions Finalized

<p align="center"><img src="images/screenshot65.png" alt="NTFS Permissions Finalized" width="80%"/></p>

> Final ACL: Administrators, SYSTEM, FinanceData.

#### 🔹 66. Open Server Manager

<p align="center"><img src="images/screenshot66.png" alt="Open Server Manager" width="80%"/></p>

> Dashboard on SRV01.

#### 🔹 67. File and Storage Services

<p align="center"><img src="images/screenshot67.png" alt="File and Storage Services" width="80%"/></p>

> Shares page lists the FinanceData share.

#### 🔹 68. Open Share Properties

<p align="center"><img src="images/screenshot68.png" alt="Open Share Properties" width="80%"/></p>

> General / Permissions / Settings / Customize pages.

#### 🔹 69. Settings Tab

<p align="center"><img src="images/screenshot69.png" alt="Settings Tab" width="80%"/></p>

> Advanced options: enumeration and encryption.

#### 🔹 70. Enable Access-Based Enumeration

<p align="center"><img src="images/screenshot70.png" alt="Enable Access-Based Enumeration" width="80%"/></p>

> Users cannot even *see* folders they can't access.

#### 🔹 71. Enable SMB Encryption

<p align="center"><img src="images/screenshot71.png" alt="Enable SMB Encryption" width="80%"/></p>

> Protects data in transit against sniffing.

#### 🔹 72. Apply Share Settings

<p align="center"><img src="images/screenshot72.png" alt="Apply Share Settings" width="80%"/></p>

> Defense-in-depth active on the share.

#### 🔹 73. Share Settings Verified

<p align="center"><img src="images/screenshot73.png" alt="Share Settings Verified" width="80%"/></p>

> Enumeration + encryption remain enabled.

#### 🔹 74. Create a Test File

<p align="center"><img src="images/screenshot74.png" alt="Create a Test File" width="80%"/></p>

> `Q4_Report.txt` created for permission testing.

#### 🔹 75. Add Confidential Content

<p align="center"><img src="images/screenshot75.png" alt="Add Confidential Content" width="80%"/></p>

> Simulated sensitive finance data.

#### 🔹 76. Test File Saved

<p align="center"><img src="images/screenshot76.png" alt="Test File Saved" width="80%"/></p>

> Ready for client-side access tests.

#### 🔹 77. Login to CL01 as john

<p align="center"><img src="images/screenshot77.png" alt="Login to CL01 as john" width="80%"/></p>

> `CORP\john` — authorized FinanceData member.

#### 🔹 78. Open the Share by UNC Path

<p align="center"><img src="images/screenshot78.png" alt="Open the Share by UNC Path" width="80%"/></p>

> `\\192.168.10.25\FinanceData` accessed over SMB.

#### 🔹 79. Access Granted for john

<p align="center"><img src="images/screenshot79.png" alt="Access Granted for john" width="80%"/></p>

> ✅ Authorized user can read the data.

#### 🔹 80. Access Denied for martin

<p align="center"><img src="images/screenshot80.png" alt="Access Denied for martin" width="80%"/></p>

> ❌ Unauthorized user blocked — RBAC works as designed.

</details>

<details>
<summary><b>📏 Phase 4 — Microsoft Security Compliance Toolkit</b> &nbsp;·&nbsp; Steps 81–108</summary>

<br>

#### 🔹 81. Open the MSCT Download Page

<p align="center"><img src="images2/screenshot81.png" alt="Open the MSCT Download Page" width="80%"/></p>

> Official Microsoft security baselines.

#### 🔹 82. Select Files to Download

<p align="center"><img src="images2/screenshot82.png" alt="Select Files to Download" width="80%"/></p>

> Windows 10 / Server 2019 baseline, Policy Analyzer, LGPO.

#### 🔹 83. LGPO.zip Selected

<p align="center"><img src="images2/screenshot83.png" alt="LGPO.zip Selected" width="80%"/></p>

> Local Group Policy Object tool for CLI baseline application.

#### 🔹 84. Download Both Files

<p align="center"><img src="images2/screenshot84.png" alt="Download Both Files" width="80%"/></p>

> Policy Analyzer + LGPO downloaded.

#### 🔹 85. Extract Policy Analyzer

<p align="center"><img src="images2/screenshot85.png" alt="Extract Policy Analyzer" width="80%"/></p>

> Extracted to `C:\MSCT\PolicyAnalyzer`.

#### 🔹 86. Locate LGPO.exe

<p align="center"><img src="images2/screenshot86.png" alt="Locate LGPO.exe" width="80%"/></p>

> Found in the Downloads folder.

#### 🔹 87. LGPO Copied to System32

<p align="center"><img src="images2/screenshot87.png" alt="LGPO Copied to System32" width="80%"/></p>

> Runs from any directory via PATH.

#### 🔹 88. Open Policy Analyzer Folder

<p align="center"><img src="images2/screenshot88.png" alt="Open Policy Analyzer Folder" width="80%"/></p>

> Contains `PolicyAnalyzer.exe` and rule files.

#### 🔹 89. Policy Analyzer Launched

<p align="center"><img src="images2/screenshot89.png" alt="Policy Analyzer Launched" width="80%"/></p>

> Empty until baseline is imported.

#### 🔹 90. Policy Analyzer Main Window

<p align="center"><img src="images2/screenshot90.png" alt="Policy Analyzer Main Window" width="80%"/></p>

> Add / Compare / View options.

#### 🔹 91. Add Files from GPOs

<p align="center"><img src="images2/screenshot91.png" alt="Add Files from GPOs" width="80%"/></p>

> File → Add files from GPO(s).

#### 🔹 92. Browse to the Baseline GPOs

<p align="center"><img src="images2/screenshot92.png" alt="Browse to the Baseline GPOs" width="80%"/></p>

> Select all baseline GPO folders.

#### 🔹 93. Select Policies to Import

<p align="center"><img src="images2/screenshot93.png" alt="Select Policies to Import" width="80%"/></p>

> Import as one rules file.

#### 🔹 94. Confirm the Import

<p align="center"><img src="images2/screenshot94.png" alt="Confirm the Import" width="80%"/></p>

> Dialog confirms import.

#### 🔹 95. Import Completed

<p align="center"><img src="images2/screenshot95.png" alt="Import Completed" width="80%"/></p>

> Microsoft recommended baseline loaded.

#### 🔹 96. Name the Rules File

<p align="center"><img src="images2/screenshot96.png" alt="Name the Rules File" width="80%"/></p>

> Saved as `Server2019_Baseline`.

#### 🔹 97. Compare Effective State

<p align="center"><img src="images2/screenshot97.png" alt="Compare Effective State" width="80%"/></p>

> Baseline vs the real configuration of DC01.

#### 🔹 98. Understanding the Result Columns

<p align="center"><img src="images2/screenshot98.png" alt="Understanding the Result Columns" width="80%"/></p>

> Differences = not following the baseline.

#### 🔹 99. Open the View Menu

<p align="center"><img src="images2/screenshot99.png" alt="Open the View Menu" width="80%"/></p>

> Filtering options for the long settings list.

#### 🔹 100. Show Only Differences

<p align="center"><img src="images2/screenshot100.png" alt="Show Only Differences" width="80%"/></p>

> Highlights every misconfiguration.

#### 🔹 101. Misconfigurations Highlighted

<p align="center"><img src="images2/screenshot101.png" alt="Misconfigurations Highlighted" width="80%"/></p>

> Yellow cells, e.g. audit policies set to *No Auditing*.

#### 🔹 102. Open the Local_Script Folder

<p align="center"><img src="images2/screenshot102.png" alt="Open the Local_Script Folder" width="80%"/></p>

> Contains `BaselineLocalInstall.ps1`.

#### 🔹 103. Set the Execution Policy

<p align="center"><img src="images2/screenshot103.png" alt="Set the Execution Policy" width="80%"/></p>

> `Set-ExecutionPolicy RemoteSigned` to allow the script.

#### 🔹 104. Apply the Baseline on DC01

<p align="center"><img src="images2/screenshot104.png" alt="Apply the Baseline on DC01" width="80%"/></p>

> `.\BaselineLocalInstall.ps1 -WS2019DomainController`

#### 🔹 105. Open Local Group Policy Editor

<p align="center"><img src="images2/screenshot105.png" alt="Open Local Group Policy Editor" width="80%"/></p>

> `gpedit.msc` to verify.

#### 🔹 106. Navigate to Account Policies

<p align="center"><img src="images2/screenshot106.png" alt="Navigate to Account Policies" width="80%"/></p>

> Password and Account Lockout policies.

#### 🔹 107. Password Policy Verified

<p align="center"><img src="images2/screenshot107.png" alt="Password Policy Verified" width="80%"/></p>

> Stricter values confirm baseline applied.

#### 🔹 108. Baseline Applied on SRV01

<p align="center"><img src="images2/screenshot108.png" alt="Baseline Applied on SRV01" width="80%"/></p>

> Same process repeated; DC01 + SRV01 aligned.

</details>

<details>
<summary><b>🔄 Phase 5 — Patch Management with BatchPatch</b> &nbsp;·&nbsp; Steps 109–126</summary>

<br>

#### 🔹 109. Run the BatchPatch Installer

<p align="center"><img src="images2/screenshot109.png" alt="Run the BatchPatch Installer" width="80%"/></p>

> Prompts for `PsExec.exe` location.

#### 🔹 110. Locate PsExec

<p align="center"><img src="images2/screenshot110.png" alt="Locate PsExec" width="80%"/></p>

> Browse PSTools folder.

#### 🔹 111. Select PsExec.exe

<p align="center"><img src="images2/screenshot111.png" alt="Select PsExec.exe" width="80%"/></p>

> Used for remote command execution.

#### 🔹 112. Register File Extensions

<p align="center"><img src="images2/screenshot112.png" alt="Register File Extensions" width="80%"/></p>

> Yes — BatchPatch installed.

#### 🔹 113. Add Hosts to BatchPatch

<p align="center"><img src="images2/screenshot113.png" alt="Add Hosts to BatchPatch" width="80%"/></p>

> Grid → Add Hosts.

#### 🔹 114. Hosts Added

<p align="center"><img src="images2/screenshot114.png" alt="Hosts Added" width="80%"/></p>

> Lab machines listed in the grid.

#### 🔹 115. Specify Alternate Credentials

<p align="center"><img src="images2/screenshot115.png" alt="Specify Alternate Credentials" width="80%"/></p>

> Administrator account for remote access.

#### 🔹 116. Enter Domain Credentials

<p align="center"><img src="images2/screenshot116.png" alt="Enter Domain Credentials" width="80%"/></p>

> Domain `corp.local`; session-only.

#### 🔹 117. Credentials Saved

<p align="center"><img src="images2/screenshot117.png" alt="Credentials Saved" width="80%"/></p>

> Ready to scan.

#### 🔹 118. Select All Hosts

<p align="center"><img src="images2/screenshot118.png" alt="Select All Hosts" width="80%"/></p>

> `Ctrl+A` → Windows Updates → Check for Available Updates.

#### 🔹 119. Check for Available Updates

<p align="center"><img src="images2/screenshot119.png" alt="Check for Available Updates" width="80%"/></p>

> Queries Windows Update on every target.

#### 🔹 120. Scan Results

<p align="center"><img src="images2/screenshot120.png" alt="Scan Results" width="80%"/></p>

> Missing updates per machine.

#### 🔹 121. Select Machines for Patching

<p align="center"><img src="images2/screenshot121.png" alt="Select Machines for Patching" width="80%"/></p>

> Download and Install selected.

#### 🔹 122. Reboot Option

<p align="center"><img src="images2/screenshot122.png" alt="Reboot Option" width="80%"/></p>

> Automatically reboot if required.

#### 🔹 123. Confirm Installation

<p align="center"><img src="images2/screenshot123.png" alt="Confirm Installation" width="80%"/></p>

> Remote install begins.

#### 🔹 124. Patching in Progress

<p align="center"><img src="images2/screenshot124.png" alt="Patching in Progress" width="80%"/></p>

> Live status per machine.

#### 🔹 125. Open Task Scheduler

<p align="center"><img src="images2/screenshot125.png" alt="Open Task Scheduler" width="80%"/></p>

> Automate patching.

#### 🔹 126. Weekly Patch Schedule

<p align="center"><img src="images2/screenshot126.png" alt="Weekly Patch Schedule" width="80%"/></p>

> Weekly recurrence — hands-free patching.

</details>

<details>
<summary><b>🛡️ Phase 6 — Windows Hardening</b> &nbsp;·&nbsp; Steps 127–137</summary>

<br>

#### 🔹 127. Open Local Security Policy

<p align="center"><img src="images2/screenshot127.png" alt="Open Local Security Policy" width="80%"/></p>

> `secpol.msc` on DC01.

#### 🔹 128. Open Security Options

<p align="center"><img src="images2/screenshot128.png" alt="Open Security Options" width="80%"/></p>

> Locate the NTLM-related setting.

#### 🔹 129. LAN Manager Authentication Level

<p align="center"><img src="images2/screenshot129.png" alt="LAN Manager Authentication Level" width="80%"/></p>

> Controls allowed challenge-response protocol.

#### 🔹 130. Send NTLMv2 Only

<p align="center"><img src="images2/screenshot130.png" alt="Send NTLMv2 Only" width="80%"/></p>

> *Send NTLMv2 response only. Refuse LM & NTLM* — repeated on SRV01.

#### 🔹 131. Check Firewall Profiles

<p align="center"><img src="images2/screenshot131.png" alt="Check Firewall Profiles" width="80%"/></p>

> `Get-NetFirewallProfile | Select Name, Enabled`

#### 🔹 132. Allow SMB from the Internal Network

<p align="center"><img src="images2/screenshot132.png" alt="Allow SMB from the Internal Network" width="80%"/></p>

> Rule allows SMB only from `192.168.10.0/24`.

#### 🔹 133. Block SMB from Everywhere Else

<p align="center"><img src="images2/screenshot133.png" alt="Block SMB from Everywhere Else" width="80%"/></p>

> *Block SMB External* rule for `0.0.0.0/0`.

#### 🔹 134. Enable Advanced Audit Policies

<p align="center"><img src="images2/screenshot134.png" alt="Enable Advanced Audit Policies" width="80%"/></p>

> `auditpol` success/failure auditing (e.g. DS Access).

#### 🔹 135. More Audit Categories Enabled

<p align="center"><img src="images2/screenshot135.png" alt="More Audit Categories Enabled" width="80%"/></p>

> Logons, account changes, policy changes.

#### 🔹 136. Verify Audit Policy

<p align="center"><img src="images2/screenshot136.png" alt="Verify Audit Policy" width="80%"/></p>

> `auditpol /get /category:*`

#### 🔹 137. Audit Policy on SRV01

<p align="center"><img src="images2/screenshot137.png" alt="Audit Policy on SRV01" width="80%"/></p>

> Both servers now produce detailed security logs.

</details>

<details>
<summary><b>📡 Phase 7 — Wazuh SIEM Deployment & Agents</b> &nbsp;·&nbsp; Steps 138–164</summary>

<br>

#### 🔹 138. Wazuh Server Installation Begins

<p align="center"><img src="images2/screenshot138.jpeg" alt="Wazuh Server Installation Begins" width="80%"/></p>

> Open-source SIEM/XDR on IDS01.

#### 🔹 139. Update the System

<p align="center"><img src="images2/screenshot139.jpeg" alt="Update the System" width="80%"/></p>

> `sudo apt update && sudo apt upgrade -y`

#### 🔹 140. Install curl

<p align="center"><img src="images2/screenshot140.jpeg" alt="Install curl" width="80%"/></p>

> Needed to fetch the installer.

#### 🔹 141. Run the Wazuh Install Script

<p align="center"><img src="images2/screenshot141.jpeg" alt="Run the Wazuh Install Script" width="80%"/></p>

> `wazuh-install.sh -a` — all-in-one (indexer, manager, Filebeat, dashboard).

#### 🔹 142. Wazuh Admin Credentials

<p align="center"><img src="images2/screenshot142.jpeg" alt="Wazuh Admin Credentials" width="80%"/></p>

> Admin password printed — save it.

#### 🔹 143. Open the Dashboard in the Browser

<p align="center"><img src="images2/screenshot143.jpeg" alt="Open the Dashboard in the Browser" width="80%"/></p>

> HTTPS with self-signed certificate warning.

#### 🔹 144. Wazuh Login Page

<p align="center"><img src="images2/screenshot144.png" alt="Wazuh Login Page" width="80%"/></p>

> Log in with admin credentials.

#### 🔹 145. Wazuh Dashboard Overview

<p align="center"><img src="images2/screenshot145.jpeg" alt="Wazuh Dashboard Overview" width="80%"/></p>

> Security events, FIM, vulnerability detection.

#### 🔹 146. Open https://192.168.10.60

<p align="center"><img src="images2/screenshot146.png" alt="Open https://192.168.10.60" width="80%"/></p>

> IDS01 dashboard from a lab machine.

#### 🔹 147. Accept the Certificate Warning

<p align="center"><img src="images2/screenshot147.png" alt="Accept the Certificate Warning" width="80%"/></p>

> Safe inside the controlled lab.

#### 🔹 148. Logged In as admin

<p align="center"><img src="images2/screenshot148.png" alt="Logged In as admin" width="80%"/></p>

> SIEM ready to receive agent data.

#### 🔹 149. Connect Agents to the Server

<p align="center"><img src="images2/screenshot149.png" alt="Connect Agents to the Server" width="80%"/></p>

> An agent is needed on each endpoint.

#### 🔹 150. Search for Wazuh Agent

<p align="center"><img src="images2/screenshot150.jpeg" alt="Search for Wazuh Agent" width="80%"/></p>

> Use the official Wazuh site.

#### 🔹 151. Open the Wazuh Website

<p align="center"><img src="images2/screenshot151.jpeg" alt="Open the Wazuh Website" width="80%"/></p>

> Windows installer (MSI) guide.

#### 🔹 152. Download the Windows Installer

<p align="center"><img src="images2/screenshot152.png" alt="Download the Windows Installer" width="80%"/></p>

> MSI saved to Downloads.

#### 🔹 153. Run the Agent Installer

<p align="center"><img src="images2/screenshot153.jpeg" alt="Run the Agent Installer" width="80%"/></p>

> Administrator rights required.

#### 🔹 154. Install the Agent

<p align="center"><img src="images2/screenshot154.png" alt="Install the Agent" width="80%"/></p>

> Setup wizard completed.

#### 🔹 155. Open Wazuh Agent Manager

<p align="center"><img src="images2/screenshot155.png" alt="Open Wazuh Agent Manager" width="80%"/></p>

> Manager IP must be set.

#### 🔹 156. Enter the Manager IP

<p align="center"><img src="images2/screenshot156.jpeg" alt="Enter the Manager IP" width="80%"/></p>

> `192.168.10.60`

#### 🔹 157. Start the Agent Service

<p align="center"><img src="images2/screenshot157.jpeg" alt="Start the Agent Service" width="80%"/></p>

> Key generated, status Running.

#### 🔹 158. Agent Registered

<p align="center"><img src="images2/screenshot158.jpeg" alt="Agent Registered" width="80%"/></p>

> Appears in the Wazuh dashboard.

#### 🔹 159. Add the Wazuh GPG Key

<p align="center"><img src="images2/screenshot159.png" alt="Add the Wazuh GPG Key" width="80%"/></p>

> Linux: lets apt verify Wazuh packages.

#### 🔹 160. Add the Wazuh Repository

<p align="center"><img src="images2/screenshot160.png" alt="Add the Wazuh Repository" width="80%"/></p>

> Official Wazuh apt repo added.

#### 🔹 161. Install the Wazuh Agent

<p align="center"><img src="images3/screenshot161.png" alt="Install the Wazuh Agent" width="80%"/></p>

> `sudo apt-get install wazuh-agent -y`

#### 🔹 162. Configure the Manager IP

<p align="center"><img src="images3/screenshot162.png" alt="Configure the Manager IP" width="80%"/></p>

> `sed` replaces `MANAGER_IP` in `ossec.conf`.

#### 🔹 163. Start and Enable the Agent

<p align="center"><img src="images3/screenshot163.png" alt="Start and Enable the Agent" width="80%"/></p>

> `systemctl start` + `enable wazuh-agent`.

#### 🔹 164. Active Agent in Dashboard

<p align="center"><img src="images3/screenshot164.png" alt="Active Agent in Dashboard" width="80%"/></p>

> Linux endpoint **Active** — logs flowing.

</details>

<details>
<summary><b>🔍 Phase 8 — Sysmon & Log Collection</b> &nbsp;·&nbsp; Steps 165–182</summary>

<br>

#### 🔹 165. Download Sysmon

<p align="center"><img src="images3/screenshot165.png" alt="Download Sysmon" width="80%"/></p>

> Sysinternals Sysmon package.

#### 🔹 166. Extract the Sysmon Package

<p align="center"><img src="images3/screenshot166.png" alt="Extract the Sysmon Package" width="80%"/></p>

> ZIP extracted on the Windows VM.

#### 🔹 167. Sysmon Package Contents

<p align="center"><img src="images3/screenshot167.png" alt="Sysmon Package Contents" width="80%"/></p>

> `Sysmon64.exe` for 64-bit systems.

#### 🔹 168. Download the SwiftOnSecurity Config

<p align="center"><img src="images3/screenshot168.jpeg" alt="Download the SwiftOnSecurity Config" width="80%"/></p>

> Community configuration with strong defaults.

#### 🔹 169. Copy the Config File

<p align="center"><img src="images3/screenshot169.png" alt="Copy the Config File" width="80%"/></p>

> Placed next to `sysmon64.exe`.

#### 🔹 170. Save Config as XML

<p align="center"><img src="images3/screenshot170.png" alt="Save Config as XML" width="80%"/></p>

> `.xml` extension as Sysmon expects.

#### 🔹 171. Install Sysmon with the Config

<p align="center"><img src="images3/screenshot171.png" alt="Install Sysmon with the Config" width="80%"/></p>

> `sysmon64.exe -i sysmonconfig-export.xml`

#### 🔹 172. Accept the License Agreement

<p align="center"><img src="images3/screenshot172.png" alt="Accept the License Agreement" width="80%"/></p>

> EULA accepted.

#### 🔹 173. Installation Successful

<p align="center"><img src="images3/screenshot173.jpeg" alt="Installation Successful" width="80%"/></p>

> Sysmon service running.

#### 🔹 174. View Sysmon Logs in Event Viewer

<p align="center"><img src="images3/screenshot174.png" alt="View Sysmon Logs in Event Viewer" width="80%"/></p>

> Microsoft → Windows → Sysmon → Operational.

#### 🔹 175. Locate the Agent Config File

<p align="center"><img src="images3/screenshot175.jpeg" alt="Locate the Agent Config File" width="80%"/></p>

> `ossec.conf` in `ossec-agent`.

#### 🔹 176. Add Sysmon as a Log Source

<p align="center"><img src="images3/screenshot176.png" alt="Add Sysmon as a Log Source" width="80%"/></p>

> `localfile` entry for the Sysmon Operational log.

#### 🔹 177. Restart the Wazuh Agent

<p align="center"><img src="images3/screenshot177.jpeg" alt="Restart the Wazuh Agent" width="80%"/></p>

> Loads the new log source.

#### 🔹 178. Wazuh Rules for Sysmon Events

<p align="center"><img src="images3/screenshot178.jpeg" alt="Wazuh Rules for Sysmon Events" width="80%"/></p>

> FileCreate, ProcessAccess, RegistryEvent hits.

#### 🔹 179. Sysmon Data Confirmed in Wazuh

<p align="center"><img src="images3/screenshot179.jpeg" alt="Sysmon Data Confirmed in Wazuh" width="80%"/></p>

> Process creation / image load events visible.

#### 🔹 180. Open Agent Log Configuration

<p align="center"><img src="images3/screenshot180.png" alt="Open Agent Log Configuration" width="80%"/></p>

> Review `localfile` on another agent.

#### 🔹 181. Verify Localfile Entries

<p align="center"><img src="images3/screenshot181.png" alt="Verify Localfile Entries" width="80%"/></p>

> Block complete; config saved.

#### 🔹 182. Restart the Wazuh Service

<p align="center"><img src="images3/screenshot182.png" alt="Restart the Wazuh Service" width="80%"/></p>

> Log collection active on all agents.

</details>

<details>
<summary><b>🎯 Phase 9 — Custom Detection Rules & MITRE ATT&CK</b> &nbsp;·&nbsp; Steps 183–189</summary>

<br>

#### 🔹 183. Open local_rules.xml

<p align="center"><img src="images3/screenshot183.png" alt="Open local_rules.xml" width="80%"/></p>

> `/var/ossec/etc/rules/local_rules.xml`

#### 🔹 184. Add Custom Detection Rules

<p align="center"><img src="images3/screenshot184.png" alt="Add Custom Detection Rules" width="80%"/></p>

> Rules for the lab's attack techniques.

#### 🔹 185. Restart the Wazuh Manager

<p align="center"><img src="images3/screenshot185.png" alt="Restart the Wazuh Manager" width="80%"/></p>

> Required to load custom rules.

#### 🔹 186. Verify Rules Loaded Without Errors

<p align="center"><img src="images3/screenshot186.png" alt="Verify Rules Loaded Without Errors" width="80%"/></p>

> No syntax errors in the log.

#### 🔹 187. Open Wazuh Dashboard for MITRE

<p align="center"><img src="images3/screenshot187.png" alt="Open Wazuh Dashboard for MITRE" width="80%"/></p>

> `https://192.168.10.60`

#### 🔹 188. Open the MITRE ATT&CK Menu

<p align="center"><img src="images3/screenshot188.png" alt="Open the MITRE ATT&CK Menu" width="80%"/></p>

> Left navigation → MITRE ATT&CK.

#### 🔹 189. ATT&CK Matrix View

<p align="center"><img src="images3/screenshot189.png" alt="ATT&CK Matrix View" width="80%"/></p>

> Baseline view before simulations.

</details>

<details>
<summary><b>⚔️ Phase 10 — Attack Simulation from Kali</b> &nbsp;·&nbsp; Steps 190–214</summary>

<br>

#### 🔹 190. Update Attacker Tools

<p align="center"><img src="images3/screenshot190.png" alt="Update Attacker Tools" width="80%"/></p>

> `apt-get update && upgrade` on Kali.

#### 🔹 191. Install CrackMapExec

<p align="center"><img src="images3/screenshot191.png" alt="Install CrackMapExec" width="80%"/></p>

> SMB enumeration and credential attacks.

#### 🔹 192. Install Hydra

<p align="center"><img src="images3/screenshot192.png" alt="Install Hydra" width="80%"/></p>

> Brute-force tool.

#### 🔹 193. Install python3-impacket

<p align="center"><img src="images3/screenshot193.png" alt="Install python3-impacket" width="80%"/></p>

> SMB / network protocol tools.

#### 🔹 194. Verify Tool Versions

<p align="center"><img src="images3/screenshot194.png" alt="Verify Tool Versions" width="80%"/></p>

> `cme --version`

#### 🔹 195. Verify Hydra Help Output

<p align="center"><img src="images3/screenshot195.png" alt="Verify Hydra Help Output" width="80%"/></p>

> `hydra -h | head -5`

#### 🔹 196. Network Reconnaissance Scan

<p align="center"><img src="images3/screenshot196.png" alt="Network Reconnaissance Scan" width="80%"/></p>

> `nmap -sn 192.168.10.0/24` — **T1046** Network Service Discovery.

#### 🔹 197. Detailed Scan of DC01

<p align="center"><img src="images3/screenshot197.png" alt="Detailed Scan of DC01" width="80%"/></p>

> `nmap -sV -sC -O 192.168.10.10`

#### 🔹 198. Full Port Scan

<p align="center"><img src="images3/screenshot198.png" alt="Full Port Scan" width="80%"/></p>

> `nmap -A -p- 192.168.10.10 --max-retries 2`

#### 🔹 199. SMB OS Discovery on SRV01

<p align="center"><img src="images3/screenshot199.png" alt="SMB OS Discovery on SRV01" width="80%"/></p>

> `smb-os-discovery` on ports 139/445.

#### 🔹 200. Check Wazuh for Scan Alerts

<p align="center"><img src="images3/screenshot200.png" alt="Check Wazuh for Scan Alerts" width="80%"/></p>

> Security Events filtered by agent DC01.

#### 🔹 201. SMB Share and User Enumeration

<p align="center"><img src="images3/screenshot201.png" alt="SMB Share and User Enumeration" width="80%"/></p>

> `smb-enum-shares,smb-enum-users` — **T1021.002**.

#### 🔹 202. Enumerate Shares on SRV01

<p align="center"><img src="images3/screenshot202.png" alt="Enumerate Shares on SRV01" width="80%"/></p>

> FinanceData hidden from anonymous enumeration.

#### 🔹 203. CrackMapExec Network Sweep

<p align="center"><img src="images3/screenshot203.png" alt="CrackMapExec Network Sweep" width="80%"/></p>

> `crackmapexec smb 192.168.10.0/24`

#### 🔹 204. Enumerate Shares with CrackMapExec

<p align="center"><img src="images3/screenshot204.png" alt="Enumerate Shares with CrackMapExec" width="80%"/></p>

> Access-based enumeration limits what appears.

#### 🔹 205. Enumerate Users with CrackMapExec

<p align="center"><img src="images3/screenshot205.png" alt="Enumerate Users with CrackMapExec" width="80%"/></p>

> Simulated user-list building — should alert.

#### 🔹 206. Confirm FinanceData Is Protected

<p align="center"><img src="images3/screenshot206.png" alt="Confirm FinanceData Is Protected" width="80%"/></p>

> RBAC + auditing work together.

#### 🔹 207. Prepare the Password List

<p align="center"><img src="images3/screenshot207.png" alt="Prepare the Password List" width="80%"/></p>

> Wordlist for the martin brute-force test.

#### 🔹 208. Hydra Brute Force Against SMB

<p align="center"><img src="images3/screenshot208.png" alt="Hydra Brute Force Against SMB" width="80%"/></p>

> `hydra -l martin -P /tmp/passwords.txt smb://192.168.10.10` — **T1110**.

#### 🔹 209. CrackMapExec Brute Force

<p align="center"><img src="images3/screenshot209.png" alt="CrackMapExec Brute Force" width="80%"/></p>

> Cross-validates Hydra's results.

#### 🔹 210. Check Event Viewer for Failed Logons

<p align="center"><img src="images3/screenshot210.png" alt="Check Event Viewer for Failed Logons" width="80%"/></p>

> Event ID **4625** (failed logon) and **4740** (lockout).

#### 🔹 211. Test Multiple Accounts with One Password

<p align="center"><img src="images3/screenshot211.png" alt="Test Multiple Accounts with One Password" width="80%"/></p>

> Password-spraying style test.

#### 🔹 212. Authenticated Share Check

<p align="center"><img src="images3/screenshot212.png" alt="Authenticated Share Check" width="80%"/></p>

> john's valid credentials reveal FinanceData.

#### 🔹 213. Attempt Administrator Credentials

<p align="center"><img src="images3/screenshot213.png" alt="Attempt Administrator Credentials" width="80%"/></p>

> Failed guess → strong admin password.

#### 🔹 214. Check Logged-On Users

<p align="center"><img src="images3/screenshot214.png" alt="Check Logged-On Users" width="80%"/></p>

> What an attacker learns with valid creds.

</details>

<details>
<summary><b>🚨 Phase 11 — Active Response (Detection → Prevention)</b> &nbsp;·&nbsp; Steps 215–218</summary>

<br>

#### 🔹 215. Open ossec.conf for Active Response

<p align="center"><img src="images3/screenshot215.png" alt="Open ossec.conf for Active Response" width="80%"/></p>

> `/var/ossec/etc/ossec.conf` on the manager.

#### 🔹 216. Add the Active Response Configuration

<p align="center"><img src="images3/screenshot216.png" alt="Add the Active Response Configuration" width="80%"/></p>

> Block attacking IP on high-severity rules.

#### 🔹 217. Restart the Manager to Apply Active Response

<p align="center"><img src="images3/screenshot217.png" alt="Restart the Manager to Apply Active Response" width="80%"/></p>

> `sudo systemctl restart wazuh-manager`

#### 🔹 218. Verify Active Response Is Configured

<p align="center"><img src="images3/screenshot218.png" alt="Verify Active Response Is Configured" width="80%"/></p>

> `grep -A5 active-response ossec.conf`

</details>

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
