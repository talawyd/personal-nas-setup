🖥️ Personal NAS & Private Cloud Storage System

A self-hosted private cloud storage solution built on Windows 10, accessible across macOS, iOS, and Android using Tailscale VPN — with no open ports and full security hardening.


📋 Project Overview
Instead of paying for third-party cloud services like Google Drive or iCloud, I built a fully private, self-hosted NAS (Network Attached Storage) system using a spare 1TB HDD connected to a Windows 10 desktop. The system is securely accessible from anywhere in the world using Tailscale's encrypted mesh VPN — without exposing any ports to the public internet.

🛠️ Hardware Used
ComponentSpecServerWindows 10 Desktop PCPrimary Storage1TB HDD (NAS Drive)Backup Storage1TB HDD (Local Backup)Client 1MacBook (256GB)Client 2iPhone (128GB)

🏗️ System Architecture
                    TAILSCALE MESH VPN
                  (Encrypted Tunnel Layer)
                          │
          ┌───────────────┼───────────────┐
          │               │               │
   Windows PC        MacBook         iPhone
   (NAS Host)        (Client)        (Client)
   SMB Share      Finder + SMB    Files App + SMB
          │
    ┌─────┴─────┐
    │           │
  E: Drive    Backup
 CloudStorage  Drive
  (NAS HDD)  (Local Only)

📁 Folder Structure
CloudStorage (E:)
│
├── Notes/
├── Videos/
├── Documents/
├── Media/
└── Backups/

⚙️ Setup Process
Phase 1 — Drive Preparation

Formatted 1TB HDD as NTFS with volume label CloudStorage
Created organised folder structure for notes, videos, documents and media
Assigned dedicated drive letters to clearly separate OS drive, NAS drive and backup drive

Phase 2 — Windows Network Sharing

Enabled SMB file sharing on the NAS drive via Advanced Sharing settings
Configured share name as CloudStorage
Enabled Network Discovery and File & Printer Sharing in Network and Sharing Center
Set NTFS Security permissions with correct user access levels

Phase 3 — Remote Access via Tailscale VPN

Installed Tailscale on Windows PC, MacBook and iPhone
Signed into the same Tailscale account across all devices
Connected all devices into a private encrypted mesh network
Used Tailscale IP address for SMB connections instead of local network IP — enabling access from anywhere in the world without open ports

Phase 4 — macOS Connection

Connected MacBook via Finder using smb://[TAILSCALE-IP]/CloudStorage
Installed Mounty for NTFS to enable write access from macOS to NTFS formatted drive

Phase 5 — iOS Connection

Connected iPhone via native Files app using SMB protocol over Tailscale
Installed VLC for mobile to stream video files directly from NAS
Installed Owlfiles for automatic camera roll backup to NAS


🔒 Security Hardening
Tailscale Admin Portal
PolicyConfigurationAdmin Session TimeoutAuto logout on inactivityDevice ApprovalManual approval required for all new devicesKey ExpiryDevices must re-authenticate after set number of daysACL PolicyJSON-based Access Control — only authorised account can access any deviceSSHTailscale SSH enabled for encrypted terminal access
ACL Policy (JSON)
json{
  "acls": [
    {
      "action": "accept",
      "src": ["[REDACTED]@gmail.com"],
      "dst": ["[REDACTED]@gmail.com:*"]
    }
  ],
  "ssh": [
    {
      "action": "accept",
      "src": ["[REDACTED]@gmail.com"],
      "dst": ["[REDACTED]@gmail.com"],
      "users": ["autogroup:nonroot"]
    }
  ],
  "tagOwners": {
    "tag:nas": ["[REDACTED]@gmail.com"]
  }
}
Windows System Hardening
Security MeasureDetailsWindows Firewall RuleSMB port 445 restricted to Tailscale IP range 100.64.0.0/10 onlySMB1 DisabledLegacy vulnerable protocol disabled via PowerShellStrong Password16+ character complex password on Windows user accountMFAMulti-factor authentication enabled on Tailscale login providerC: Drive ProtectionOS drive never shared — network share restricted to NAS drive onlyNTFS PermissionsCorrectly scoped security permissions on NAS drive only

🔧 Technologies Used

Windows 10 — NAS host operating system
SMB Protocol — File sharing protocol
Tailscale VPN — Encrypted mesh network for secure remote access
NTFS — File system for NAS drive
Windows Defender Firewall — Network traffic control
PowerShell — SMB1 deprecation
Mounty for NTFS — macOS write access to NTFS
iOS Files App — iPhone NAS access
Owlfiles — Automatic iPhone camera backup
VLC Mobile — Video streaming from NAS


🌐 Key Technical Decisions
Why Tailscale instead of port forwarding?
Port forwarding exposes your machine directly to the public internet. Tailscale creates an encrypted peer-to-peer tunnel between devices with no open ports — meaning there is no attack surface exposed to the internet whatsoever.
Why NTFS instead of exFAT?
NTFS supports proper file permissions, security policies and large file sizes. exFAT has no permission system making it unsuitable for a shared network drive.
Why keep the backup drive off the network?
A backup that is network accessible can be wiped in the same attack or accident that corrupts the primary drive. Keeping the backup local-only ensures data survives network-based incidents.

📚 Lessons Learned

Windows has two separate permission systems — Share Permissions and NTFS Security Permissions — both must be configured correctly for network access to work
Never apply permission changes to the OS drive (C:) — always verify which drive you are modifying before making changes
A static local IP is unnecessary when using Tailscale since it assigns its own permanent address to each device
Tethered internet connections create a separate network subnet — devices on different networks cannot reach each other via SMB without a VPN layer


🚀 Future Improvements

 Set up automated backup schedule from NAS drive to backup drive using Windows Task Scheduler
 Add a second NAS drive for RAID-style redundancy
 Set up Syncthing for automatic Notes folder sync across all devices
 Explore Plex Media Server on top of the NAS for video streaming
 Add UPS (Uninterruptible Power Supply) to protect against power cuts


⚠️ Disclaimer
All sensitive information including IP addresses, device names, usernames and account emails have been redacted from this documentation. Never publish your actual Tailscale IPs, machine names or credentials in any public repository.

📄 License
This project is open for reference and learning purposes. Feel free to use this documentation as a guide for your own NAS setup.
