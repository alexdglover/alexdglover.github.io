---
title: "Powering a Raspberry Pi"
date: 2013-12-17 18:23:08.000000000 -06:00
categories:
- How-to Guides
- Questions for the Community
- Electronics Projects
tags:
- backpower
- pi
- power
- raspberry pi
header:
  teaser: /assets/raspberryPiPolyfuse.png
excerpt_separator: <!--more-->
---
<p>I recently started building out a combination NAS and HTPC on a Raspberry Pi and came across some interesting information about powering Raspberry Pi's. While this isn't a project post per se, I thought it was interesting enough to share and wanted to send this out as sort of a PSA for the users out there who don't know the potential danger of "backpowering" a Raspberry Pi.</p>
<!--more-->
<p>For those who don't already have one, here are a few suggestions for where to get a Raspberry Pi.</p>
<h3>Where to get a Raspberry Pi</h3>
<p>If you have some of the peripherals (SD card, HDMI cable, micro USB power supply), you can get started with a standalone Model B Raspberry Pi for as low as $35.</p>
<ul>
<li><a href="http://www.newark.com/raspberry-pi/raspbrry-modb-512m/model-b-assembled-board-only/dp/43W5302?COM=raspi-group" target="_blank">Element14</a> - $35</li>
<li><a href="http://www.alliedelec.com/lp/120626raso/" target="_blank">AlliedElec</a> - $35</li>
<li><a href="https://www.sparkfun.com/products/11546" target="_blank">SparkFun</a> - $39.95</li>
<li><a href="http://www.amazon.com/gp/product/B009SQQF9C/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B009SQQF9C&linkCode=as2&tag=alexdgloverwo-20" target="_blank">Amazon</a> (Prime eligible) - $40.60</li>
<li><a href="http://dx.com/p/raspberry-pi-project-board-green-267945?Utm_rid=52026082&Utm_source=affiliate" target="_blank">DealExtreme</a> (free shipping) - $55.70</li>
</ul>
<p>If you would rather get a starter kit, the prices are pretty reasonable and usually includes an SD card pre-loaded with the Raspberry Pi New Out Of Box Software (NOOBS). Here are two good starter kits.</p>
<ul>
<li><a href="http://www.amazon.com/gp/product/B008XVAVAW/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B008XVAVAW&linkCode=as2&tag=alexdgloverwo-20" target="_blank">Raspberry Pi Model B, Case, Micro USB Power Supply, SD Card with NOOBS, and HDMI Cable via Amazon</a> (Prime eligible) - $61.95</li>
<li><a href="http://www.canakit.com/raspberry-pi-starter-kit.html" target="_blank">Raspberry Pi Model B, Case, Micro USB Power Supply, SD Card with NOOBS, and HDMI Cable via Canakit</a> - $69.95</li>
</ul>
<h3>Power Consumption</h3>
<p>As an Arduino die hard, I didn't think I really needed a Raspberry Pi for anything. I was sold once I realized I could run all of my home web server functions on the 3.5 watt ARM processor instead of 85 watt Intel chip in my desktop. The Raspberry Pi will actually pay for itself in about 4 months.</p>
<p><strong>Utility cost of running home server on full x86 desktop (Intel dual core, ATI Radeon HD GPU, 3 HDDs) [high estimate]</strong></p>
<p>(Power draw x hours per week x weeks per year x cost per kilowatt hour) / (1000 watts per kW x 0.85 efficiency x 12 months a year) = estimated cost per month</p>
<p>(150 watts x 168 hours x 52 weeks x $0.10 per kW hour) / 1000 watts per kW x 0.85 efficiency x 12 months a year) = $12.85 per month</p>
<p><strong>Utility cost of running home server on Raspberry Pi and one external hard drive (5 watts for Raspberry Pi, 15 watts for HDD under load) [high estimate]</strong></p>
<p>(20 watts x 168 hours x 52 weeks x $0.10 per kW hour) / 1000 watts per kW x 0.85 efficiency x 12 months a year) = $1.71 per month</p>
<p><strong>Approximate cost savings: $11.14 per month</strong></p>
<h3>Powering the Pi and the Dangers of "Backpowering"</h3>
<p>The Pi only has two USB ports, so obviously many folks incorporate a powered USB hub to expand its capabilities. At some point, someone discovered that the powered hubs were actually supplying power to the Pi via the uplink between the hub and the Pi. Now you can power your peripherals and the Pi from one outlet. Cool right?</p>
<p style="text-align: center;"><img class="aligncenter size-full wp-image-718" alt="no" src="{{ "/assets/no.jpg" | absolute_url }}" width="500" height="450" /></p>
<p><!--more--></p>
<p>It's pretty cool until you accidentally fry your Pi or start a fire. The power supply connection on the Pi includes a polyfuse to protect against overvoltages (see diagram below).</p>
<p style="text-align: center;"><a href="http://www.raspberrypi.org/wp-content/uploads/2012/10/Raspberry-Pi-R2.0-Schematics-Issue2.2_027.pdf"><img class="aligncenter size-full wp-image-720" alt="Wiring schematic showing polyfuse" src="{{ "/assets/raspberryPiPolyfuse.png" | absolute_url }}" width="604" height="350" /></a></p>
<p>&nbsp;</p>
<p>The USB data connections do not have an inline polyfuse! If you want to run your Pi off of a powered USB hub, you'll need to do two things.</p>
<ol>
<li>Acquire a powered USB hub that specifically does NOT supply 'backpower' via the datalink/uplink/data port. If you want to play it safe, check out the <a href="http://elinux.org/RPi_Powered_USB_Hubs" target="_blank">list of compatible powered USB hubs</a> that have been tested by the community. I chose to use the <a href="http://www.amazon.com/gp/product/B003Z4G3I6/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B003Z4G3I6&linkCode=as2&tag=alexdgloverwo-20" target="_blank">Plugable 7-port USB hub</a>.</li>
<li>Run a <a href="http://www.amazon.com/gp/product/B002HMWQE2/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B002HMWQE2&linkCode=as2&tag=alexdgloverwo-20" target="_blank">USB-to-micro-USB cable</a> from one of the outputs on your USB hub back to the power input connection on the Pi. This will properly route your +5v power through the polyfuse, protecting your Pi.</li>
</ol>
<p>See picture below for a visual explanation.</p>
<p style="text-align: center;"><a href="{{ "/assets/photo-11.jpg" | absolute_url }}"><img class="aligncenter size-large wp-image-725" alt="Powering Raspberry Pi with Powered USB" src="{{ "/assets/photo-11-1024x944.jpg" | absolute_url }}" width="620" height="571" /></a></p>
<p>Obviously you still need to plug your powered USB hub into a power source. Now your Raspberry Pi will run off of the powered USB hub, allowing you to safely run your Pi and your peripherals from one power supply.</p>
<p>Anyway, just wanted to share a couple of lessons learned while getting started and highlight the details behind "backpowering."</p>
<p>How do you power your Pi?</p>
