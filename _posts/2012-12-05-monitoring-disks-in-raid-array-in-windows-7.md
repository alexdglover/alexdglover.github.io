---

title: Monitoring Disks in RAID Array in Windows 7
date: 2012-12-05 08:30:11.000000000 -06:00



categories:
- How-to Guides
- Utilities And Other Useful Things
tags:
- monitoring
- raid
- windows 7
meta:
  _wpas_done_all: '1'
  _publicize_pending: '1'
  jabber_published: '1354718459'
  publicize_twitter_user: alexdglover
  _wpas_done_1477652: '1'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:297624509;b:1;}}
  email_notification: '1354718460'
  _wpas_done_1477650: '1'
  reddit: a:2:{s:5:"count";i:0;s:4:"time";i:1355790895;}
---
<p>For many folks whose motherboards don't support RAID or just want a simple, easy to implement RAID solution, software RAID is often the best solution. In Windows 7, Microsoft finally got around to providing out-of-the-box software <a href="http://en.wikipedia.org/wiki/RAID" target="_blank">RAID</a> capabilities (apparently you could <a href="http://www.tomshardware.com/reviews/windowsxp-make-raid-5-happen,925-2.html" target="_blank">do it in Windows XP</a>, but not without some real leg work). Only problem is Microsoft didn't include a notification or alarm mechanism to deal with disk failures. The only notification is a passive entry in the Event Log, which is not obvious for basic users and not really convenient for anyone. This effectively defeats the purpose of RAID as a mechanism for data redundancy, as your disks could fail one by one until all of your data was unrecoverable, and you as the user wouldn't realize until it was too late.</p>
<p><!--more Click to see my solution --></p>
<p>Fortunately there are several free disk monitoring applications that can fill this void Microsoft left for us. I've chosen <a href="http://www.passmark.com/products/diskcheckup.htm" target="_blank">DiskCheckup</a> for one important reason - it has the ability to send email notifications when there's an issue (what good is a desktop alert, if you're not on your desktop?). DiskCheckup uses the <a href="http://en.wikipedia.org/wiki/S.M.A.R.T." target="_blank">S.M.A.R.T</a> values from your hard drives to look for potential issues and notifies you if they exceed pre-set thresholds. Downloading and installing DiskCheckup is quite easy, but there are a few important configuration steps I wanted to highlight:</p>
<ol>
<li><span style="line-height:13px;">After installing, open DiskCheckup. Note - by default, it doesn't start the application after installation or after a reboot. Something to keep in mind if  you want it running all the time</span></li>
<li>Click Configuration in the bottom left
<p style="text-align: center;"><a href="{{ "/assets/?attachment_id=292" | absolute_url }}" rel="attachment wp-att-292"><img class="aligncenter size-medium wp-image-292" alt="alexdglover_DiskCheckup_1" src="{{ site.baseurl }}/assets/alexdglover_diskcheckup_1.png?w=300" height="231" width="300" /></a>
</p>
</li>
<li>Choose the Send e-mail notification radio button and click Settings
<p style="text-align: center;"><a href="{{ "/assets/?attachment_id=293" | absolute_url }}" rel="attachment wp-att-293"><img class="aligncenter size-medium wp-image-293" alt="alexdglover_DiskCheckup_2" src="{{ site.baseurl }}/assets/alexdglover_diskcheckup_2.png?w=216" height="300" width="216" /></a></p>
</li>
<li>Enter the email address where you want notifications sent in the top right and click Add. For outgoing mail server settings, use your ISPs SMTP relay information. For example, here's a <a href="http://www.realifewebdesigns.com/web-resources/pop3-smtp-news-servers/timewarner.asp" target="_blank">good list of Time Warner Cable SMTP relay servers</a>. With a little bit of Googling, you should be able to find your relay. With my ISPs SMTP relay, I do not have to provide credentials. Also note, this screen allows you to specify a port - don't bother trying to use SMTP relays with TLS enforced, DiskCheckup doesn't support it. You can also optionally add a "From" address if you want, although that may cause issues with your SMTP relay.
<p style="text-align: center;"><a href="{{ "/assets/?attachment_id=294" | absolute_url }}" rel="attachment wp-att-294"><img class="aligncenter size-medium wp-image-294" alt="alexdglover_DiskCheckup_3" src="{{ site.baseurl }}/assets/alexdglover_diskcheckup_3.png?w=300" height="248" width="300" /></a></p>
<p>That's it - fire off a test email to make sure it works, and click OK! An email will be sent if any of the S.M.A.R.T. values or the temperature exceeds their respective thresholds. You now have a vigilant application looking after your hard drives!</p>
</li>
</ol>
