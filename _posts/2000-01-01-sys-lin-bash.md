---
layout: post
title: sys / lin / bash
category: sys
parent: cheatsheets
modified_date: 2023-07-19
permalink: /sys/lin/bash
---
<!-- vscode-markdown-toc -->
* [loop](#loop)
* [sed](#sed)
* [find](#find)

<!-- vscode-markdown-toc-config
	numbering=false
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

## <a name='loop'></a>loop
```
# change echo with enum4linux, nmap, ... 
for i in {1..254}; do echo 172.17.135.$i >> tt.txt; i=$i+1; done
```

## <a name='sed'></a>sed
```sh
#? getting-start sed
#
#? sed print line by its number X
sed -n Xp toto.txt

#? sed print file section from line number X to Y
sed -n "X,Y/*/p" toto.txt

#? sed print file section from line number X to EOF
sed -n "X,/*/p" toto.txt

# sed insert a space between 2 IPs - solving copy/paste issue of nessus reports
sed '%s/.([0-9]+)192./.\1 192./g' 

### <a name='oneliners'></a>oneliners
# oneliner howto - grep into jmvwork.xyz cheatsheets
# takes the pattern as first argument
# takes the file as second argument
# if multiple match, print howto for the first pattern found
function howto { line=`grep -n $1 $2 |sed -n 1p |cut -f1 -d":"`; sed -n "${line},/.?/p" $2 |awk '$0 ~/^#$/ { exit; } $0 { print;}'; } 
function gstart { line=`grep -n "^#? getting-start" |grep -n $1 $2 |sed -n 1p |cut -f1 -d":"`; sed -n "${line},/.?/p" $2 |awk '$0 ~/^```.*$/ { exit; } $0 { print;}'; } 

# oneliner howto example 1
howto "docker install spiderfoot" 2021-10-26-sys-cli-docker.md

# oneliner howto example 2
howto "# oneliner .* ex" 2021-10-26-sys-cli-lin.md

```
## <a name='find'></a>find
```sh
### <a name='findpdffilescreatedlas24hoursinDownloadsdirectory:'></a>find pdf files created las 24 hours in Downloads directory:
find ~/Dowloads -iname *.pdf -a -ctime 1

## <a name='identifyfileswiththesuidsgidpermissions'></a>identify files with the suid, sgid permissions
find / -perm +6000 -type f -exec ls -ld {} \; > setuid.txt &
find / -perm +4000 -user root -type f -

```