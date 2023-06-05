---
layout: post
title: MicroSoft Defender
parent: EDR
category: EDR
grand_parent: Cheatsheets
modified_date: 2023-06-03
permalink: /edr/defender
---

<!-- vscode-markdown-toc -->
* [enum](#enum)
* [kql](#kql)

<!-- vscode-markdown-toc-config
	numbering=false
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

## <a name='enum'></a>enum
```powershell
# defender 
# defender: check if Defender is enabled
Get-MpComputerStatus
Get-MpComputerStatus | Select AntivirusEnabled
# defender: check if defensive modules are enabled
Get-MpComputerStatus | Select RealTimeProtectionEnabled, IoavProtectionEnabled,AntispywareEnabled | FL
# defender: check if tamper protection is enabled
Get-MpComputerStatus | Select IsTamperProtected,RealTimeProtectionEnabled | FL
```

## <a name='kql'></a>kql

* KQL queries over the field ```InitiatingProcessFileName```, table ```DeviceProcessEvents```:

```
DeviceProcessEvents
| where InitiatingProcessFileName =~ $_KEYWORD_$
```

| loots | $_KEYWORD_$ |
|-------|----------------------------|
| Kubernetes | "kubectl" |
| Container Registry X | "docker " |  
| DB x | "sqlplus" |
| psexec | "psexec " |