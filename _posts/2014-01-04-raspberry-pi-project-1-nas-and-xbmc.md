---
title: 'Raspberry Pi Project 1: NAS and XBMC'
date: 2014-01-04 16:08:19.000000000 -06:00
categories:
- Fun IT Projects
- How-to Guides
tags:
- htpc
- nas
- ntfs
- project
- raspberry pi
- raspbian
- samba
- xbmc
---
<p>The Raspberry Pi makes a great HTPC or media PC, whatever you want to call it, because of its low cost, low power consumption, quiet, and small form factor. As I was getting ready to start my Raspberry Pi build, I realized that the Pi could take over a few other functions that my desktop does, including NAS, at a fraction of the cost (see my previous post about <a title="Powering a Raspberry Pi" href="http://alexdglover.com/powering-a-raspberry-pi/">Powering a Pi</a> and related utility savings). But could I modify a RaspXBMC build to also run Samba or some other network file sharing? Could XBMC be installed on Raspbian with less than a thousand calls to aptitude? Could XBMC and Samba run simultaneously on a Pi without making video playback unwatchable? That's what I hoped to find out.</p>
<p>For context, I started with a <a href="http://www.amazon.com/gp/product/B008XVAVAW/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B008XVAVAW&linkCode=as2&tag=alexdgloverwo-20" target="_blank">Raspberry Pi starter kit</a> from Amazon. For storage, I am using a <a href="http://www.amazon.com/gp/product/B004IYJX16/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B004IYJX16&linkCode=as2&tag=alexdgloverwo-20" target="_blank">Fantom 2TB external hard drive</a>. After some preliminary research, I decided to start with Raspbian and try to install XBMC on top. For this walk-through, I'll assume you've got your Raspberry Pi powered, connected to your router, and that you have gotten Raspbian installed. If you need help getting started, check out the <a href="http://www.raspberrypi.org/wp-content/uploads/2012/04/quick-start-guide-v2_1.pdf" target="_blank">Raspberry Pi Quick Start Guide</a>.</p>
<h2>Part 1 - NAS</h2>
<h3>Part 1.1 - NTFS-3G</h3>
<p>If you don't plan to use NTFS as your filesystem for your Pi storage, you can skip this step!</p>
<p>If you do want to use NTFS, and you want to be able to read AND write to the drive, you need to install NTFS support:</p>

```shell
sudo apt-get install ntfs-3g
```

<p>If you're starting with a brand new external hard drive, you'll need to create the partition and filesystem.</p>

```shell
pi@raspberrypi ~ $ sudo fdisk /dev/sda

Command (m for help): n
Partition type:
   p   primary (1 primary, 0 extended, 3 free)
   e   extended
Select (default p): p
Partition number (1-4, default 2): 1
Command (m for help): t
Selected partition 1
Hex code (type L to list codes):7
Command (m for help): w
...
(disk is formatted)
...
pi@raspberrypi ~ $ mkfs.ntfs /dev/sda1
```

<h3>Part 1.2 - Automounting with fstab</h3>
<p>In Raspbian (and every flavor of Linux I've worked with so far), all filesystems that should be mounted at boot are identified in the fstab file (/etc/fstab). The fstab file can be configured a million ways, so I'll just share my configuration and explain.</p>

```shell
proc            /proc           proc    defaults          0       0
/dev/mmcblk0p5  /boot           vfat    defaults          0       2
/dev/mmcblk0p6 /               ext4    defaults,noatime  0       1
# a swapfile is not a swap partition, so no using swapon|off from here on, use  dphys-swapfile swap[on|off]  for that

#/dev/sda1 - the 2TB Fantom Drive
/dev/sda1       /media/1TB      ntfs-3g defaults        0       0
```

<p>The first four lines were created automatically by the Raspbian install, we'll leave those alone. We're only interested in the last line that starts with /dev/sda1, that's the line I've manually added to the configuration.</p>
<p>BUT WHAT DOES IT ALL MEAN???  The columns of the fstab file (courtesy of the <a href="https://wiki.archlinux.org/index.php/fstab" target="_blank">ArchLinux wiki</a>) are</p>

`# <file system>  <dir>  <type>  <options>  <dump>  <pass>`

**`<file system>`** - the partition or storage device to be mounted. In my case, "/dev/sda1"

**`<dir>`** - the mountpoint where `<file system>` is mounted to. In my case, "/media/1TB"

**`<type>`** - the file system type of the partition or storage device to be mounted. In my case, it is "ntfs-3g"

**`<options>`** - mount options for the filesystem. In my case, I chose "defaults" which includes mounting the filesystem as read-write with no <a href="http://en.wikipedia.org/wiki/Umask" target="_blank">umasks</a> for permissions/access

**`<dump>`** - not needed in our case, just use 0 (zero)

**`<pass>`** - used by <a title="Fsck" href="https://wiki.archlinux.org/index.php/Fsck">fsck</a> to decide which order filesystems are to be checked. You can set this to 0 (zero)

<p>So my configuration file says to mount /dev/sda1 on /media/1TB as an NTFS filesystem, using default mount options, and never run fsck on this drive.</p>
<p>Now that you have an example, implementing is pretty easy.</p>

1. Make a backup of your fstab file

   `sudo cp /etc/fstab /var/fstab`
2. Open fstab in your favorite text editor

   `sudo vim /etc/fstab`
3. Add the lines to the configuration (see example above) to automount your drives
4. Save and close the fstab file
5. Test your configuration by executing a mount all command

   `mount -a`

<p>If your drive correctly mounted to the mount point you specified (/media/1TB) and it is mounted as read-write, you're golden.</p>
<p>If you are using multiple drives, then simply add additional lines to the fstab to cover all of the drives you want to automount. Now our storage will automatically mount read-write at startup.</p>
<h3>Part 1.3 - Samba</h3>
<p>In my build, I wanted to share out any drives mounted in /media/ in case I added drives in the future. Additionally, accessibility rather than security was the priority for me. Because of that I set up Samba to allow anonymous access to any device on my LAN. If you're cool with that approach, follow along. If not, you may have to do some research on how to set up Samba to your liking.</p>


1. Install samba

   `sudo apt-get install samba`
2. Open the samba config file in your favorite text editor

   `sudo vim /etc/samba/smb.conf`
3. Find the line that says "security = user" and update the file with the following lines

   ```shell
   # "security = user" is always a good idea. This will require a Unix account
   # in this server for every user accessing the server. See
   # /usr/share/doc/samba-doc/htmldocs/Samba3-HOWTO/ServerType.html
   # in the samba-doc package for details.
      security = share
      guest account = nobody
   ```

4. Scroll to the bottom of the Samba configuration file and add a new section for your new share. See my configuration for example

   ```shell
   [shared]
        comment = Shared 1TB drive on Raspberry Pi
        path = /media/1TB/
        browseable = yes
        read only = no
        guest ok = yes
   ```

5. Test your configuration by executing `testparm` and examining the output

   ```shell
   pi@raspberrypi ~ $ testparm
   Load smb config files from /etc/samba/smb.conf
   rlimit_max: increasing rlimit_max (1024) to minimum Windows limit (16384)
   Processing section "[homes]"
   Processing section "[printers]"
   Processing section "[print$]"
   Processing section "[shared]"
   WARNING: The security=share option is deprecated
   Loaded services file OK.
   Server role: ROLE_STANDALONE
   Press enter to see a dump of your service definitions
   ```

6. Assuming your test went well, reload samba

   `sudo /etc/init.d/samba reload`

7. On another device, try to mount the share. On Windows, you can just open the start menu and type "\\yourservername" or "\\your.server.IP.address" On a Mac, open Finder, go to the "Go" dropdown menu at the top, and choose connect to server and enter "smb://yourservername" or "smb://your.server.IP.address" If prompted for a user account, choose Guest.

{% include figure image_path="/assets/mac_finder_connect_to_server.gif" alt="Finder - Connect to Server" caption="Courtesy of Mobilefish.com" %}

<p>OK, this is already over 1000 words long so let's take a breather and continue with XBMC in Part 2.</p>

<h2>Part 2 - XBMC</h2>
<h3>Part 2.1 - Installing XBMC</h3>
<p>Installing XBMC is usually pretty easy, but since we're using Raspbian we can't use the normal packages. Fortunately for me and anyone who is trying to accomplish this, <a href="http://michael.gorven.za.net/" target="_blank">Michael Gorven</a> has already figured this out for us and even hosts the packages we need. Go ahead and open a new window or tab and browse to <a href="http://michael.gorven.za.net/raspberrypi/xbmc">http://michael.gorven.za.net/raspberrypi/xbmc</a> . The author has already done a great job of documenting this process so I won't duplicate it here. Once your installation is finished, test that you can start XBMC by executing 'sudo xbmc-standalone' and seeing if XBMC appears on your display.</p>
<p>This is perfect, except that XBMC is now tied to our session. Now if you're directly connected to the Pi with a keyboard, this is irrelevant. If, like me, you're doing all of your Pi work via SSH, XBMC will die once you terminate your SSH session. Let's set up XBMC as a service so we can start/stop it gracefully and allow it to run after our SSH session ends. Additionally, if you're having issues with XBMC not starting at boot, setting up a service can ensure that XBMC is started for the right run levels.</p>
<h3>Part 2.2 - Setting up XBMC as a Service</h3>
<p>The <a href="http://wiki.xbmc.org/index.php?title=Main_Page" target="_blank">XBMC Wiki</a> has a good article for <a href="http://wiki.xbmc.org/index.php?title=HOW-TO:Install_XBMC_for_Linux#Add_a_new_init_script" target="_blank">how to set up XBMC as a service</a>, but I want to expand on it a little bit.</p>

1. Create the init script file
   ```
   vim /etc/init.d/xbmc
   ```

2. Paste the following code into your vim session:
   ```shell
   #! /bin/sh

   ### BEGIN INIT INFO
   # Provides:          xbmc
   # Required-Start:    $all
   # Required-Stop:     $all
   # Default-Start:     2 3 4 5
   # Default-Stop:      0 1 6
   # Description:       starts instance of XBMC using start-stop-daemon and xinit
   # Short-Description: starts instance of XBMC
   ### END INIT INFO

   ############### EDIT ME ##################

   # path to xinit exec
   DAEMON=/usr/bin/xinit

   # startup args
   DAEMON_OPTS=" /usr/bin/xbmc --standalone -- :0"

   # script name
   NAME=xbmc

   # app name
   DESC=XBMC

   # user
   RUN_AS=pi

   # Path of the PID file
   PID_FILE=/var/run/xbmc.pid

   ############### END EDIT ME ##################

   test -x $DAEMON || exit 0

   set -e

   case "$1" in
     start)
           echo "Starting $DESC"
           start-stop-daemon --start -c $RUN_AS --background --pidfile $PID_FILE  --make-pidfile --exec $DAEMON -- $DAEMON_OPTS
           ;;
     stop)
           echo "Stopping $DESC"
           start-stop-daemon --stop --pidfile $PID_FILE
           ;;

     status)
           echo "Checking status of $DESC..."
           PID=$(cat $PID_FILE)
           # This line is for debugging purposes only
           # echo "PID is $PID"
           if ps -p $PID > /dev/null ; then
                   echo "XBMC is running with PID $PID"
           else
                   echo "XBMC doesn't seem to be running"
           fi
           ;;

     restart|force-reload)
           echo "Restarting $DESC"
           start-stop-daemon --stop --pidfile $PID_FILE
           sleep 5
           start-stop-daemon --start -c $RUN_AS --background --pidfile $PID_FILE  --make-pidfile --exec $DAEMON -- $DAEMON_OPTS
           ;;
     *)
           N=/etc/init.d/$NAME
           echo "Usage: $N {start|stop|restart|force-reload}" >&2
           exit 1
           ;;
   esac

   exit 0
   ```

   Save and quit out of vim. There are a few differences between my init script and the one provided on the XBMC wiki. For one, my "runas" user is set to "pi" - you'll want to set this to whatever user you installed XBMC with to avoid permissions issues. Additionally, my init script will take "status" as an argument to the init script and will return information about whether or not XBMC is running.

3. Change the script's permissions to make it executable
   ```
   chmod a+x /etc/init.d/xbmc
   ```

4. Test that the init script is working:
   ```shell
   pi@raspberrypi ~ $ sudo /etc/init.d/xbmc start
   Starting XBMC
   pi@raspberrypi ~ $ /etc/init.d/xbmc status
   Checking status of XBMC...
   XBMC is running with PID 10986
   pi@raspberrypi ~ $ /etc/init.d/xbmc stop
   Stopping XBMC
   pi@raspberrypi ~ $ /etc/init.d/xbmc status
   Checking status of XBMC...
   XBMC doesn't seem to be running
   ```

5. If you want XBMC to start when the OS boots, update the rc.d scripts. Unless you have a specific use case, using the "defaults" argument will work for you.
   ```shell
   sudo update-rc.d xbmc defaults
   ```

6. If you want to be thorough, restart your Raspberry Pi and verify that your disk has been mounted (via fstab from Part 1) and that XBMC has started.
7. You're done. Time to enjoy.

<p style="text-align: center;"><a href="http://alexdglover.com/wp-content/uploads/2014/01/abedcoolcoolcoo.gif"><img class="aligncenter size-medium wp-image-757" alt="abed_cool_cool_cool" src="{{ site.baseurl }}/assets/abedcoolcoolcoo-300x133.gif" width="300" height="133" /></a></p>

<h3>Part 2.3 - XBMC Remote</h3>
<p>If you're going to manage your XBMC/NAS/Pi remotely, I would also suggest downloading the <a href="http://wiki.xbmc.org/index.php?title=Official_XBMC_Remote" target="_blank">XBMC Remote App</a> for your Adroid or iPhone. To enable remote control on XBMC, go to System -> Settings -> Services -> Remote Control and enable both "Allow programs on this system to control XBMC" and "Allow programs on other systems to control XBMC."</p>

{% include figure image_path="/assets/500px-Settings.services.remote_control.png" alt="XBMC remote control" caption="Courtesy of xbmc.org" %}

<p>Assuming you're on the same wireless network, you should be able to connect to the XBMC with your remote app using your Pi's IP address and the default username and password of 'xbmc.'</p>
<p>Well at nearly 2000 words, this has been a whopper of a post. Hopefully this write up has helped you one way or another, and let me know if you have any questions. Enjoy your new combination NAS and HTPC.</p>
