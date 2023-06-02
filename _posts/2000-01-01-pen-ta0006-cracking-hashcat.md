---
layout: post
title: TA0006 Credentials Access - Cracking with Hashcat
parent: Pentesting
category: Pentesting
grand_parent: Cheatsheets
modified_date: 2023-06-02
permalink: /pen/hashcat
---

<!-- vscode-markdown-toc -->
1. [hashkiller.io dictionary](#hashkiller.iodictionary)
2. [SecLists Passwords](#SecListsPasswords)
3. [SecLists Leaked Databases](#SecListsLeakedDatabases)
4. [Diff between 2 Dictionaries](#Diffbetween2Dictionaries)
5. [Map cracked passwords to relative accounts](#Mapcrackedpasswordstorelativeaccounts)

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

##  1. <a name='hashkiller.iodictionary'></a>hashkiller.io dictionary

* [haskiller.io/download](https://hashkiller.io/download)

line numbers of password dictionaries:
```
└─$ wc -l
```

##  2. <a name='SecListsPasswords'></a>SecLists Passwords

* [SecLists](https://github.com/danielmiessler/SecLists)

line numbers of password dictionaries:
```
└─$ wc -l SecLists/Passwords/*.* |sort -r
 24001867 total
  5446758 SecLists/Passwords/dutch_common_wordlist.txt
  5189454 SecLists/Passwords/xato-net-10-million-passwords.txt
  4322843 SecLists/Passwords/dutch_passwordlist.txt
  3721224 SecLists/Passwords/openwall.net-all.txt
  1652903 SecLists/Passwords/bt4-password.txt
  1471056 SecLists/Passwords/darkc0de.txt
  1000000 SecLists/Passwords/xato-net-10-million-passwords-1000000.txt
   755995 SecLists/Passwords/xato-net-10-million-passwords-dup.txt
   172696 SecLists/Passwords/mssql-passwords-nansh0u-guardicore.txt
   100000 SecLists/Passwords/xato-net-10-million-passwords-100000.txt
    47603 SecLists/Passwords/Most-Popular-Letter-Passes.txt
    19994 SecLists/Passwords/richelieu-french-top20000.txt
    13431 SecLists/Passwords/months.txt
    12877 SecLists/Passwords/SCRABBLE-hackerhouse.tgz
    12645 SecLists/Passwords/probable-v2-top12000.txt
    10000 SecLists/Passwords/xato-net-10-million-passwords-10000.txt
     9999 SecLists/Passwords/darkweb2017-top10000.txt
     9604 SecLists/Passwords/Keyboard-Combinations.txt
     6240 SecLists/Passwords/days.txt
     5390 SecLists/Passwords/seasons.txt
     5000 SecLists/Passwords/richelieu-french-top5000.txt
     3629 SecLists/Passwords/unkown-azul.txt
     3502 SecLists/Passwords/scraped-JWT-secrets.txt
     1759 SecLists/Passwords/common_corporate_passwords.lst
     1575 SecLists/Passwords/probable-v2-top1575.txt
     1041 SecLists/Passwords/cirt-default-passwords.txt
     1000 SecLists/Passwords/xato-net-10-million-passwords-1000.txt
      999 SecLists/Passwords/darkweb2017-top1000.txt
      727 SecLists/Passwords/UserPassCombo-Jay.txt
      499 SecLists/Passwords/500-worst-passwords.txt
      399 SecLists/Passwords/twitter-banned.txt
      261 SecLists/Passwords/german_misc.txt
      207 SecLists/Passwords/probable-v2-top207.txt
      197 SecLists/Passwords/2020-200_most_used_passwords.txt
      100 SecLists/Passwords/xato-net-10-million-passwords-100.txt
       99 SecLists/Passwords/darkweb2017-top100.txt
       82 SecLists/Passwords/clarkson-university-82.txt
       25 SecLists/Passwords/PHP-Magic-Hashes.txt
       17 SecLists/Passwords/README.md
       10 SecLists/Passwords/xato-net-10-million-passwords-10.txt
       10 SecLists/Passwords/darkweb2017-top10.txt
        8 SecLists/Passwords/500-worst-passwords.txt.bz2
        4 SecLists/Passwords/stupid-ones-in-production.txt
        4 SecLists/Passwords/citrix.txt
        1 SecLists/Passwords/der-postillon.txt
```

##  3. <a name='SecListsLeakedDatabases'></a>SecLists Leaked Databases

line numbers of leaked databases:
```
└─$ wc -l SecLists/Passwords/Leaked-Databases/*.* |sort -r
 10373085 total
  3431316 SecLists/Passwords/Leaked-Databases/md5decryptor-uk.txt
  3132006 SecLists/Passwords/Leaked-Databases/alleged-gmail-passwords.txt
   720302 SecLists/Passwords/Leaked-Databases/000webhost.txt
   434923 SecLists/Passwords/Leaked-Databases/fortinet-2021.txt
   375853 SecLists/Passwords/Leaked-Databases/Ashley-Madison.txt
   226928 SecLists/Passwords/Leaked-Databases/honeynet2.txt
   226928 SecLists/Passwords/Leaked-Databases/honeynet-withcount.txt
   226081 SecLists/Passwords/Leaked-Databases/honeynet.txt
   213627 SecLists/Passwords/Leaked-Databases/rockyou.txt.tar.gz
   212904 SecLists/Passwords/Leaked-Databases/rockyou-withcount.txt.tar.gz
   184389 SecLists/Passwords/Leaked-Databases/phpbb-withcount.txt
   184388 SecLists/Passwords/Leaked-Databases/phpbb.txt
   184364 SecLists/Passwords/Leaked-Databases/phpbb-cleaned-up.txt
    95073 SecLists/Passwords/Leaked-Databases/muslimMatch-withcount.txt
    95072 SecLists/Passwords/Leaked-Databases/muslimMatch.txt
    59186 SecLists/Passwords/Leaked-Databases/rockyou-75.txt
    42660 SecLists/Passwords/Leaked-Databases/rockyou-70.txt
    38820 SecLists/Passwords/Leaked-Databases/tuscl.txt
    37144 SecLists/Passwords/Leaked-Databases/myspace-withcount.txt
    37126 SecLists/Passwords/Leaked-Databases/myspace.txt
    30289 SecLists/Passwords/Leaked-Databases/rockyou-65.txt
    21040 SecLists/Passwords/Leaked-Databases/rockyou-60.txt
    14235 SecLists/Passwords/Leaked-Databases/rockyou-55.txt
    12864 SecLists/Passwords/Leaked-Databases/bible-withcount.txt
    12570 SecLists/Passwords/Leaked-Databases/bible.txt
    12234 SecLists/Passwords/Leaked-Databases/singles.org-withcount.txt
    12233 SecLists/Passwords/Leaked-Databases/singles.org.txt
    11781 SecLists/Passwords/Leaked-Databases/Lizard-Squad.txt
     9437 SecLists/Passwords/Leaked-Databases/rockyou-50.txt
     8930 SecLists/Passwords/Leaked-Databases/hotmail.txt
     8348 SecLists/Passwords/Leaked-Databases/faithwriters-withcount.txt
     8345 SecLists/Passwords/Leaked-Databases/faithwriters.txt
     8089 SecLists/Passwords/Leaked-Databases/porn-unknown-withcount.txt
     8088 SecLists/Passwords/Leaked-Databases/porn-unknown.txt
     6163 SecLists/Passwords/Leaked-Databases/rockyou-45.txt
     4064 SecLists/Passwords/Leaked-Databases/youporn2012.txt
     4062 SecLists/Passwords/Leaked-Databases/youporn2012-raw.txt
     3957 SecLists/Passwords/Leaked-Databases/rockyou-40.txt
     2506 SecLists/Passwords/Leaked-Databases/rockyou-35.txt
     2351 SecLists/Passwords/Leaked-Databases/hak5.txt
     2351 SecLists/Passwords/Leaked-Databases/hak5-withcount.txt
     1904 SecLists/Passwords/Leaked-Databases/carders.cc.txt
     1556 SecLists/Passwords/Leaked-Databases/rockyou-30.txt
     1476 SecLists/Passwords/Leaked-Databases/izmy.txt
     1437 SecLists/Passwords/Leaked-Databases/NordVPN.txt
      929 SecLists/Passwords/Leaked-Databases/rockyou-25.txt
      895 SecLists/Passwords/Leaked-Databases/elitehacker.txt
      895 SecLists/Passwords/Leaked-Databases/elitehacker-withcount.txt
      512 SecLists/Passwords/Leaked-Databases/rockyou-20.txt
      249 SecLists/Passwords/Leaked-Databases/rockyou-15.txt
      100 SecLists/Passwords/Leaked-Databases/adobe100.txt
       92 SecLists/Passwords/Leaked-Databases/rockyou-10.txt
       13 SecLists/Passwords/Leaked-Databases/rockyou-05.txt
```

The tarball rockyou is 14 million lines:
```
└─$ tar xvzf SecLists/Passwords/Leaked-Databases/rockyou.txt.tar.gz
└─$ wc -l rockyou.txt
14344391 rockyou.txt
```

##  4. <a name='Diffbetween2Dictionaries'></a>Diff between 2 Dictionaries

Diff on 2 dictionaries:
```
└─$ diff Passwords/xato-net-10-million-passwords.txt Passwords/Leaked-Databases/rockyou-75.txt -u | grep "^+" > ~/diff-xato-rockyou.txt
└─$ wc -l ~/diff-xato-rockyou.txt
57897 diff-xato-rockyou.txt
```

Removes the first character ("+" added by diff):
```
#v1
sed -e 's/^.//' diff-xato-rockyou.txt
#v2
cut -c2- ~/diff-xato-rockyou.txt
```

##  5. <a name='Mapcrackedpasswordstorelativeaccounts'></a>Map cracked passwords to relative accounts

List the accounts for which the passwords were cracked: 

```sh
wget https://raw.githubusercontent.com/jomivz/jomivz.github.io/master/playbook/pen_cracked_accounts.sh
chmod +x pen_cracked_accounts.sh
./cracked_accounts.sh secretdumps.out 
```

##  5. <a name='Mapcrackedpasswordstorelativeaccounts'></a> The potfiles

Go to the path:
``` 
/home/$LOGNAME/.local/share/hashcat
```