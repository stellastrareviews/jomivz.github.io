---
layout: default
title: Miscellaneous
parent: System
grand_parent: Cheatsheets
nav_order: 2
has_children: true
---
ps -aux |pastebinit
pandoc docker.md | lynx -stdin
pdfunite infile1.pdf infile2.pdf outfile.pdf
convert  -resize 50% source.png dest.jpg
convert -resize 512x512\> secureelance_purple_484x512.png output.png
file output.png 
	output.png: PNG image data, 484 x 512, 8-bit/color RGBA, non-interlaced
