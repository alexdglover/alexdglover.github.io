---
title: Automate remote password changes with BASH and 'Expect'
date: 2012-09-15 15:13:41.000000000 -05:00
categories:
- Utilities And Other Useful Things
tags:
- bash
- expect
- script
- unix
meta:
  _wpas_done_all: '1'
  jabber_published: '1347740022'
  email_notification: '1347740023'
  _wpas_done_twitter: '1'
  _wpas_skip_1477652: '1'
  _edit_last: '1'
  _s2mail: 'yes'
  _wpas_skip_1477650: '1'
---
<p>I think this topic has been covered, in varying levels, by many people on the internet. Still, I felt like I was bashing my head against the wall trying to figure this out. To prevent head trauma to some other sys admins, I thought I'd post this.</p>
<p>First, some assumptions. Let's assume you're a sysadmin, working mainly in a LAMP environment (or at least Linux and MySQL). Let's also assume you have a reasonable number of hosts, between 5 and 50. You don't really have the right scale to warrant setting up RADIUS or TACACS authentication systems, but it is a real pain in the ass to reset passwords on a regular basis. Last, let's assume that for some good reason, you use the same password across many machines, both for the OS and DB authentication. To change all of those passwords, you would have to SSH into each box, run a couple password change commands, and exit each host.</p>
<p>Using BASH and 'Expect,' I've automated that process. For my experiment, I assumed that I wanted to change OS passwords for users root, oracle, and asm and DB passwords for root and oracle. I provide a list of hosts that are going to be updated in a control file titled 'password_change_list' There is a single shell script, 'change_password' (to invoke, just cd to the appropriate directory and type ./change_password').  Instead of taking a bunch of screenshots, I've included a quick screencapture of the script in action!</p>

{% include video id="49511130" provider="vimeo" %}

<p>In the demo, I show the contents of the password_change_list control file, 2 hosts - localhost and my dev box. Add more hosts to this file as necessary. I start the script, which first asks you to confirm that you updated the control file. Next it asks for the current password, and then for the new password. The script will then SSH to the first host and run the password change commands, then exit. The script then loops and SSH's to the next host, and so on (in the demo, you'll see it fails to log on to my dev box via SSH, incorrect password). Now, since we're entering passwords to the command line and logging the commands, the passwords will be in clear text. To address that, the script will ask if you want to obfuscate all of the clear text passwords in the log. If you choose no, it will warn you again that the passwords are in clear text. Alternatively, if you leave clear text passwords in the log file, you can run the 'obfuscate_passwords' script, provide the new password, and it will replace all instances of the password with asterisks.</p>
<p>An archive can be downloaded <a href="https://github.com/alexdglover/miniature-octo-spoon/blob/master/remotePasswordChangeUtility.tar">here</a>. Otherwise you can check out the scripts in GitHub:</p>

[GitHub repo](https://github.com/alexdglover/miniature-octo-spoon){: .btn .btn--info .btn--large}

<p>Obviously, the script will need some tweaking to suit your purpose, but I hope this helps you get started.</p>
