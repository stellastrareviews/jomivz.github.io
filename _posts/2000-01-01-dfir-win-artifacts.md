---
layout: post
title: DFIR WIN Artifacts
parent: cheatsheets
category: dfir
modified_date: 2023-06-08
permalink: /dfir/win/artifacts
---

<!-- vscode-markdown-toc -->
* [amcache](#amcache)
* [dll](#dll)
* [eventlogs](#eventlogs)
		* [eventlogs-all](#eventlogs-all)
		* [eventlogs-dns](#eventlogs-dns)
* [ntfs](#ntfs)
* [ntds-dit](#ntds-dit)
* [reg](#reg)
	* [regripper](#regripper)
	* [reg-history](#reg-history)
	* [reg-extra](#reg-extra)

<!-- vscode-markdown-toc-config
	numbering=false
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

🔥 EXHAUSTIVE ARTIFACT LISTING: [dfir.tips](https://evids.dfir.tips) 🔥

## <a name='amcache'></a>amcache

Files in column of the table are in the directory `C:\Windows\AppCompat\Programs`.

![Amcache Artifacts](/assets/images/amcache_artifacts.PNG)

**References**
- ANSSI - [CoRIIN_2019 - Analysis AmCache](https://www.ssi.gouv.fr/uploads/2019/01/anssi-coriin_2019-analysis_amcache.pdf) - 07/2019
- ANSSI - [SANS DFIR AmCache Investigation](https://www.youtube.com/watch?v=_DqTBYeQ8yA) - 02/2020 

## <a name='dll'></a>dll

## <a name='eventlogs'></a>eventlogs

Eventlogs Files :

#### <a name='eventlogs-all'></a>eventlogs-all

* All Windows Versions :

- %SystemRoot%\System32\winevt\logs\Application.evtx
- %SystemRoot%\System32\winevt\logs\Security.evtx
- %SystemRoot%\System32\winevt\logs\System.evtx
- %SystemRoot%\System32\winevt\logs\Windows Powershell.evtx

#### <a name='eventlogs-dns'></a>eventlogs-dns 

* Windows DNS Server :

1/ Are the DNS debug logs activated ?

Open a console (`cmd.exe`) and run the command: 
```
# check the parameter `dwDebugLevel`. It value must be `00006101`.
dnscmd /Info
```

2/ Where are located the DNS debug logs ?

By default, the locations for storing DNS logs are :
- %SystemRoot%\System32\Winevt\Logs\Microsoft-Windows-DNSServer%4Analytical.etl
- %SystemRoot%\System32\Dns\Dns.log

To verify it, open a console (`cmd.exe`) and run the commands:
```batch
reg query HKLM\System\CurrentControlSet\Services\DNS\Parameters
```
```powershell
Get-ChildItem -Path HKLM:\System\CurrentControlSet\Services\DNS
```

3/ How to activate the DNS logs ? How to define logs location ?

Open a console (`cmd.exe`) and run the commands:
```batch
# set the debug mode
dnscmd.exe localhost /Config /LogLevel 0x6101
# set the log file path
dnscmd.exe localhost /Config /LogFilePath "C:\Windows\System32\DNS\dns.log"
```

## <a name='ntfs'></a>ntfs

NTFS metafiles : 

- Path: \\.\C:\[SYSTEM]
- Files: $MFT, $MFTMirr, $LogFile, $Volume, $AttrDef, . , $Bitmap, $Boot, $BadClus, $Secure, $UpCase, $Extend
- [https://en.wikipedia.org/wiki/NTFS#Metafiles]() : descriptions table of the metaflies

## <a name='ntds-dit'></a>ntds-dit

NTDS.dit

systemroot\NTDS\Ntds.dit

## <a name='reg'></a>reg

**What is it ?** Files listed are the evidences to collect for the forensics. 

**Note:** There is one NTuser.dat and one UsrClass.dat per user to collect.

| **Hive** | **System Path** |
|---------------|-------------|
| HKLM\SYSTEM | %SystemRoot%\system32\config\system |
| HKLM\SAM | %SystemRoot%\system32\config\sam |
| HKLM\SECURITY | %SystemRoot%\system32\config\security |
| HKLM\SOFTWARE | %SystemRoot%\system32\config\software |
| HKLM\DEFAULT | %SystemRoot%\system32\config\default |
| HKCU\UserProfile | %UserProfile%\NTuser.dat |
| HKCU\Software\Classes | %UserProfile%\AppData\Local\Microsoft\Windows\UsrClass.dat |

### <a name='regripper'></a>regripper

* credits: [hexacorn](https://hexacorn.com/tools/3r.html)

* Confirming the asset & timezone:

| **Hive** | **Plugin** |
|---------------|-------------|
| system | compname |
| system | timezone |

* Interesting findings:

**What is it ?** In a forensics, the table below tend to help identify interesting [regripper](https://github.com/keydet89/RegRipper3.0) plugins to run on which evidences.

**Note:** refer to [heaxacorn](https://hexacorn.com/tools/3r.html) for the full listing/mapping of regripper plugins to hives.

**Note:** Run the plugins for ntuser.dat and userclass.dat, as many as the number of evidences (two per users) collected.
```bash
# Onliner to unarchive all ntuser_<username>.dat.zip collected to ntuser_<username>.dat
for i in `ls ntuser_*.dat.zip`; do unzip $i > `echo $i | sed 's/\.[^.]*$//'`; done 

# Loop example to run the autoruns plugins on all ntuser.dat, here renamed to ntuser_<username>.dat when collected
for i in `ls ntuser_*.dat`; do regripper -r $i -p autoruns; done

# Loop example to run the clsid plugins on all usrclass.dat, here renamed to usrclass_<username>.dat when collected
for i in `ls usrclass_*.dat`; do regripper -r $i -p clsid; done
```

| **Hive** | **Interesting Plugin** |
|---------------|-------------|
| ntuser.dat | autoruns |
| ntuser.dat | officedocs |
| ntuser.dat | officedocs2010 |
| ntuser.dat | rdphint |
| ntuser.dat | recentdocs |
| ntuser.dat | run |
| ntuser.dat | startup |
| usrclass.dat | clsid |
| usrclass.dat | cmd_shell_u |
| software | clsid |
| software | cmd_shell |
| software | dcom |
| software | inprocserver |
| software | run |
| system | prefetch |
| system | usbstore |
| all | sizes |

### <a name='reg-history'></a>reg-history 

* credits: [fireeye](https://www.fireeye.com/blog/threat-research/2019/01/digging-up-the-past-windows-registry-forensics-revisited.html)

**What is it ?** System and registry hives can be tampered to hide compromise / make the forensics harder. The table below lists the evidences to figure out if anti-forensics happened.

| **Evidence Type** | **User hives** | **System hives** |
|-------------------------------------|-------------------------------------|-------------------------------------|
| Registry transaction logs (.LOG)    | %UserProfile% <br /> %UserProfile%\AppData\Local\Microsoft\Windows | %SystemRoot%\system32\config\ | 
| Transactional registry transaction logs (.TxR) | %UserProfile% <br /> %UserProfile%\AppData\Local\Microsoft\Windows | %SystemRoot%\System32\config\TxR |
| Deleted entries in registry hives   | unallocated cells                                                        ||
| Backup system hives (REGBACK)       | %SystemRoot%\System32\config\RegBack                                     ||
| Hives backed up with System Restore | \\\\.\\\"System Volume Information"                                      ||

### <a name='reg-extra'></a>reg-extra

* Extra: Live collection of a locked hive :

```batch
# useful when having remote access but system handle do not allow read/copy/download 
# batch: registry hive live collection
reg save HKLM\SYSTEM system.reg
```

* Extra: live browsing a hive in CLI :
```powershell
# powershell: listing the registry hives
Get-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\hivelist\

# powershell: browsing a hive with the interpreter
cd HKLM:
```