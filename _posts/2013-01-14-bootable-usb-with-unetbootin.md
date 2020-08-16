---
title: Bootable USB with UNetbootin
date: 2013-01-14 09:26:08.000000000 -06:00
categories:
- How-to Guides
- Utilities And Other Useful Things
tags:
- bootable usb
- unetbootin
excerpt_separator: <!--more-->
---
<p>OK, so you have a netbook or ultrabook with no optical drive. Or maybe your optical drive is dead. Or maybe you're smart enough to not use optical drives in general. Whatever the situation is, you want to run or install something from a bootable USB drive.</p>
<p>There are lots of solutions out there, but nothing beats the Universal Netboot Installer, or UNetbootin. UNetbootin has a simple GUI and never gives any grief. You can use your own ISO that you've downloaded or use one of the 42 ISOs UNetbootin provides, including BackTrack, CentOS, Ubuntu, as well as several rescue and recovery utilities.</p>
<!--more-->
<p>Using UNetbootin is stupid easy. Grab a USB flash drive with enough storage to hold your ISO. Better yet - pick up a <a href="http://www.amazon.com/gp/product/B004TS1J1I/ref=as_li_ss_tl?ie=UTF8&tag=alexdgloverwo-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=B004TS1J1I" target="_blank">16GB flash drive for $10</a> and it will be big enough to hold any ISO you could ever want. Got your USB flash drive? Let's get started.</p>
<ol>
<li><span style="line-height:13px;">Plug your USB flash drive into your computer. Note the drive assignment; in Windows, it will probably be D: or E:, or some other letter. For the Unix users, your drive designation will probably be sda, sdb, sdc... sdx. Remember the drive assignment.</span></li>
<li>Go to <a href="http://unetbootin.sourceforge.net/" target="_blank">UNetbootin's page</a> and download the correct version for your OS.</li>
<li>If you have a particular ISO you want to use, download it. Otherwise if you want to use one of the included ISOs, skip this step.</li>
<li>Open UNetbootin. If you want to use one of the ISOs they provide, check the "Distribution" radio button and choose an ISO from the dropdown
<p>If you want to use your own ISO, click the "Diskimage" radio button, click the browse button " ... " and find your ISO that you downloaded.</p>
</li>
<li>Change the drive to correspond with your USB flash drive. If you can't find your drive, make sure it's mounted and then restart UNetbootin.</li>
<li>Click OK. If you chose a distribution provided by UNetbootin, it will take a fair amount of time to download the ISO before it can write to your USB drive. This is normal.</li>
<li>Once the USB has been formatted and the ISO written to it, UNetbootin will restart your computer in an attempt to boot from the USB drive. If it doesn't work, go into your BIOS settings and try changing the boot order.</li>
</ol>
<p>Donezo. Hope you found this useful.</p>
