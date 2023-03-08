---
layout: post
title: MS Defender KQL queries
parent: EDR
category: EDR
grand_parent: Cheatsheets
modified_date: 2023-02-08
permalink: /:categories/:title/
---

## <a name='practicedqueries'></a>REDTEAM

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