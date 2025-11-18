# Worksheet: Installing & Configuring Active Directory on Windows Server 2022 Core (via Remote PowerShell)

**Scenario:**  
You will install and configure **Active Directory Domain Services (AD DS)** on a **Windows Server 2022 Core** system using a **Windows 11 workstation** for remote PowerShell management.

Use the following placeholders throughout:

- `<server IP>` — IP address of the Server Core machine  
- `<wks IP>` — IP address of your Windows 11 workstation  
- `<your_domain_name>` — e.g., `example.local`  
- `<NETBIOS_NAME>` — short domain name, e.g., `EXAMPLE`  
- `<ServerName>` — a hostname for your server  
- `<gateway IP>` — your network’s gateway IP  

---
## Perequisite - Windows 11 config ##
 - Turn off firewall
 - Change network property from Public to Private
 - Enter the following command
```powershell
Enable-PSRemoting
```
## 1. Confirm Connectivity from Windows 11

```powershell
Test-NetConnection <server IP> -Port 5985
```

---

## 2. Configure Trusted Hosts (if in a Workgroup)

```powershell
Set-Item -Path WSMan:\localhost\Client\TrustedHosts -Value '<server IP>' -Force
```

```powershell
$cred = Get-Credential
```

```powershell
Test-WSMan -ComputerName <server IP> -Credential $cred
```

---

## 3. Start Remote PowerShell Session

```powershell
Enter-PSSession -ComputerName <server IP> -Credential $cred
```

---

## 4. Configure Server Core Network Settings

### 4.1 Rename the Server
```powershell
Rename-Computer -NewName '<ServerName>' -Force
```

### 4.2 Identify the Network Adapter
```powershell
Get-NetAdapter
```

### 4.3 Set a Static IP
```powershell
New-NetIPAddress `
  -InterfaceAlias 'Ethernet' `
  -IPAddress '<server IP>' `
  -PrefixLength 24 `
  -DefaultGateway '<gateway IP>'
```

### 4.4 Set DNS Server
```powershell
Set-DnsClientServerAddress `
  -InterfaceAlias 'Ethernet' `
  -ServerAddresses '<server IP>'
```

### 4.5 Reboot the Server
```powershell
Restart-Computer -Force
```

---

## 5. Install Active Directory Domain Services Role

```powershell
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
```

---

## 6. Promote the Server to a Domain Controller

```powershell
$dsrmPassword = Read-Host -AsSecureString "Enter DSRM password"
```

```powershell
Install-ADDSForest `
  -DomainName '<your_domain_name>' `
  -DomainNetbiosName '<NETBIOS_NAME>' `
  -SafeModeAdministratorPassword $dsrmPassword `
  -InstallDNS `
  -Force
```

---

## 7. Reconnect to the Domain Controller

```powershell
$domainCred = Get-Credential
Enter-PSSession -ComputerName <server IP> -Credential $domainCred
```

---

## 8. Verify AD DS and DNS Installation

```powershell
Get-Service NTDS
Get-Service DNS
Import-Module ActiveDirectory
Get-ADDomain
Get-ADForest
Get-ADDomainController -Filter *
```

---

## 9. Confirm DNS Client Settings

```powershell
Get-DnsClientServerAddress -InterfaceAlias 'Ethernet'
```

```powershell
Set-DnsClientServerAddress `
  -InterfaceAlias 'Ethernet' `
  -ServerAddresses '<server IP>'
```

---

## 10. Optional: Install RSAT on Windows 11

```powershell
Add-WindowsCapability -Name RSAT.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0 -Online
```

---

## Task Completion Checklist

| Task | Completed (✓) |
|------|:-------------:|
| WinRM connectivity tested | |
| TrustedHosts configured | |
| Remote PowerShell session established | |
| Server renamed | |
| Static IP configured | |
| DNS configured | |
| AD DS role installed | |
| Domain created / DC promoted | |
| DNS verified | |
| RSAT installed on workstation | |
