---
title: 'Google Hacking'
date: 2012-11-14 07:00:53.000000000 -06:00
categories:
- IT/Software Projects
- How-to Guides
tags:
- google
- hacking
---
<p>When I was still working at the University of Wisconsin, a co-worker of mine showed me a way to find unprotected directories indexed by Apache and web cameras. Not those kind of web cam feeds - these are usually security cameras in innocuous places, like restaurants, streets, tops of buildings... etc., that are so benign (usually) that whoever set them up decided they didn't need to be secured (or didn't know how to secure them).</p>
<p>Anyway, the <strong>way</strong> he found these was the interesting part. At the time, it was termed "Google Hacking" although now it is sometimes called "Google dorking." It is simply using Google to find unsecured web content with targeted search terms. <!--more-->You're generally looking for things like:</p>
<ul>
<li>Content that has been indexed by Apache or IIS (or any webserver for that matter), but isn't secured. This allows you to find web content that may not be intended for public consumption (i.e. there aren't links from web pages to these files). A good example would be to Google this:<br />
intitle:index.of "Apache/2.0 Server at"<br />
This will yield a LOT of results, most of which will be normal and of no value. Additional operators/terms would be needed to find anything interesting very quickly</li>
<li>Password files of various types. This seems silly, but there are a lot of password files that are on the internet. The most vulnerable are clear-text passwords (as opposed to hashed passwords). Once you have someone else's username and password, you proceed to do your malevolent activities. A good example of a Google search for clear-text passwords is:<br />
filetype:log inurl:"password.log"</li>
<li>Sensitive data such as Personally Identifiable Information (PII)  or credit card information, that is stored in clear-text</li>
<li>FTP Sites that allows anonymous connections</li>
</ul>
<p>You can actually see a lot of the world through Google hacking links to webcams. You can try this one for example:<br />
intitle:”EvoCam” inurl:”webcam.html”</p>
<p>The first result, for example, is a camera over an outdoor eating area at <a href="http://98.101.223.10:8080/webcam.html" target="_blank">The Salty Dog Cafe</a>.</p>
<p>Or you can find network printers that are accessible to the world wide web, and print funny pictures on their printer. You can find a fair number of printers with these search terms:<br />
inurl:”printer/main.html” intext:”settings”</p>
<p><span style="text-align:center;">Maybe you could print this for them:</span></p>
<p style="text-align:center;"><a href="{{ "/assets/troll-troll-face.jpg" | absolute_url }}"><img class="aligncenter  wp-image-183" title="troll-face" alt="" src="{{ site.baseurl }}/assets/troll-troll-face.jpg?w=150" height="300" width="300" /></a></p>
<p>You can find a lot of information on dorking and some good examples at the <a href="http://johnny.ihackstuff.com/ghdb/" target="_blank">Google Hacking DataBase</a>.</p>
<p>One last note - if you're doing this for malevolent purposes, you may get 'caught' - IT security folks are not dumb, so if you try to malevolently attack an organization this way, you are most likely going to find a 'honeypot.' A honeypot is an INTENTIONALLY CREATED VULNERABLE AREA to draw in rookie hackers and bust 'em or at the very least, log the malevolent attempt. If you're doing this for learning/academic worries, I wouldn't sweat it. On the other hand, should you be taking any legal advice from me?</p>
