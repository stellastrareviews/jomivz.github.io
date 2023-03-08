---
layout: post
title: Build .NET projects
category: Development
parent: Development
grand_parent: Cheatsheets  
modified_date: 2023-02-24
permalink: /:categories/:title/
---

<!-- vscode-markdown-toc -->
* 1. [Check the core version](#Checkthecoreversion)
* 2. [Install EoL version 4.5](#InstallEoLversion4.5)

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

##  1. <a name='Checkthecoreversion'></a>Check the core version
```
# V1 current version is 4.8
cd C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework
msbuild.exe -version

# V2 current version is 4.8
cd hklm:
ls SOFTWARE\Microsoft\NET Framework Setup\NDP\v4
```

##  2. <a name='InstallEoLversion4.5'></a>Install EoL version 4.5
```
choco install netfx-4.5.1-devpack
```