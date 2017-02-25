---
title: Issues with AWS CodeDeploy and CIS hardening
date: 2015-10-21 22:23:25.000000000 -05:00
categories:
- How-to Guides
tags:
- aws
- codedeploy
- umask
---
<p>I've been using <a href="https://aws.amazon.com/codedeploy/" target="_blank">AWS CodeDeploy</a> for some time now and it's really an awesome service. If you're hosting your application in AWS and not using CodeDeploy you're missing out.</p>
<p>If you want to use CodeDeploy yet but you just don't know how, check out my <a href="https://www.udemy.com/aws-codedeploy/?couponCode=GOBLs375" target="_blank">in-depth AWS CodeDeploy course</a>.</p>
<p>I had been successfully using CodeDeploy for an app at work for a few months when I was told we had to switch to using Linux AMI's that were 'hardened' to meet CIS benchmarks for security. No problem right? I redeployed the app stack with the CIS-hardened AMI and worked through a few bugs that came out of it.</p>
<p>However, when I went tried to execute my CodeDeploy deployment it died with an error claiming that it couldn't access the "patch.sh" file due to permissions. I logged on to the instance, cd'd into the CodeDeploy directory, and didn't notice anything obviously wrong with the permissions.</p>
<p>I took a look at the appspec.yml to see what might be different about this one script that was failing. Here's a mock-up of the appspec.yml similar to the one I was using:</p>
```version: 0.0
os: linux

hooks:
  BeforeInstall:
    - source: scripts/deregister-from-elb.sh
      timeout: 1200
    - source: scripts/stop-app.sh
      timeout: 1200
  AfterInstall
    - source: scripts/patch.sh
      timeout: 600
      runas: ec2-user
    - source: scripts/start-app.sh
      timeout: 600
      runas: ec2-user
    - source: scripts/register-with-elb.sh
      timeout: 1200```
<p>That's it - patch.sh is the first script executed as ec2-user. But this revision worked before, what changed?</p>
<p>One of the many changes required to meet the CIS benchmarks includes changing the <a href="https://en.wikipedia.org/wiki/Umask">umask</a> so "that files created by daemons will not be readable, writable or executable by any other than the group and owner of the daemon process..." Because the CodeDeploy daemon runs as root, any files created (including every file and script that is part of your CodeDeploy revision) are owned by root:root with permissions 750. So any script being called by a user that's not root (or in the root group) can't execute the scripts.</p>
<p>To workaround this, you can simply add another script to update the permissions of the CodeDeploy deployment-root before you execute any scripts as a non-root user.</p>
<p>Here's the updated appspec.yml:</p>
```version: 0.0
os: linux

hooks:
  BeforeInstall:
    - source: scripts/update-deployment-root-perms.sh
      timeout: 60
    - source: scripts/deregister-from-elb.sh
      timeout: 1200
    - source: scripts/stop-app.sh
      timeout: 1200
  AfterInstall
    - source: scripts/patch.sh
      timeout: 600
      runas: ec2-user
    - source: scripts/start-app.sh
      timeout: 600
      runas: ec2-user
    - source: scripts/register-with-elb.sh
      timeout: 1200
```
<p>And the update-deployment-root-perms.sh script:</p>
```#!/bin/bash

echo "Here's some meaningful message before we attempt to update the permissions of the deployment-root directory and all child files and folders"

chmod -R 755 /opt/codedeploy-agent/deployment-root

echo "Here's some meaningful message to confirm the script successfully executed the chmod command"```
<p>That's it - a simple solution for an obscure problem.</p>
<p>As always, I hope this post has been helpful and let me know if you have any questions.</p>
