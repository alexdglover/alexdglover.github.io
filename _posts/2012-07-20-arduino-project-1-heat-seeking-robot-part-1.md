---
title: 'Arduino Project 1: Heat Seeking Robot (Part 1)'
date: 2012-07-20 19:53:00.000000000 -05:00
categories:
- Fun IT Projects
tags:
- arduino
- heat seeking robot
- ir thermometer
- motor shield
- robotics
header:
  teaser: /assets/imag0002.jpg
---
**Be advised** This post is quite old ({{ page.date | date_to_string }}) and any code may be out of date. Proceed with caution.
{: .notice--warning}

<p>After finishing a <a href="http://www.cheerlights.com/" target="_blank">cheerlights</a> project for the GE Healthcare Tech Conference back in April, I had a pretty strong interest in doing more with Arduino boards. I visited many sites and read several blogs looking for inspiration for a project - I wanted to do something new, without going too crazy (this is my first stab at robotics).</p>
<p>Then I stumbled on an article about an <a href="http://bildr.org/2011/02/mlx90614-arduino/" target="_blank">IR thermometer being used with Arduino</a>. I quickly decided I was going to do something with an IR thermometer... a heat seeking missile! Oh wait. Too dangerous.</p>
<p><!--more See what project I settled on and how I built it--></p>
<p>I settled on a heat seeking vehicle. Simply put, a wheeled or tracked vehicle that would scan an area, look for areas above a certain temperature (to identify human bodies), and then drive towards that point (person).</p>
<p>Here are the supplies I bought in preparation:</p>
<ul>
<li><a href="http://www.amazon.com/gp/product/B0006O3WNW/ref=as_li_tf_tl?ie=UTF8&amp;camp=1789&amp;creative=9325&amp;creativeASIN=B0006O3WNW&amp;linkCode=as2&amp;tag=alexdgloverwo-20" target="_blank">Hitec 31055S HS-55 Economy Sub Micro Universal Servo</a> - $11</li>
<li><a href="http://www.amazon.com/gp/product/B006H06TVG/ref=as_li_tf_tl?ie=UTF8&amp;camp=1789&amp;creative=9325&amp;creativeASIN=B006H06TVG&amp;linkCode=as2&amp;tag=alexdgloverwo-20" target="_blank">Arduino Uno R3</a> - $21</li>
<li><a href="http://www.amazon.com/gp/product/B005D65LEG/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&amp;camp=1789&amp;creative=9325&amp;creativeASIN=B005D65LEG&amp;linkCode=as2&amp;tag=alexdgloverwo-20">2 Male DC Plug to 9v Battery Clip</a> - $5</li>
<li><a href="http://www.amazon.com/gp/product/B004RXKWDQ/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&amp;camp=1789&amp;creative=9325&amp;creativeASIN=B004RXKWDQ&amp;linkCode=as2&amp;tag=alexdgloverwo-20">400 point bread board and jumper wires</a> - $9</li>
<li><a href="http://www.amazon.com/gp/product/B000C8F802/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&amp;camp=1789&amp;creative=9325&amp;creativeASIN=B000C8F802&amp;linkCode=as2&amp;tag=alexdgloverwo-20">Double gearbox with 2 DC motors</a> - $9</li>
<li><a href="http://www.amazon.com/gp/product/B00061HHTK/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&amp;camp=1789&amp;creative=9325&amp;creativeASIN=B00061HHTK&amp;linkCode=as2&amp;tag=alexdgloverwo-20">Tamiya tracked vehicle kit</a> - $20</li>
<li><a href="http://www.amazon.com/gp/product/B006D85PAS/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&amp;camp=1789&amp;creative=9325&amp;creativeASIN=B006D85PAS&amp;linkCode=as2&amp;tag=alexdgloverwo-20">Motor shield for Arduino, by DF Robot</a> - $17</li>
<li><a href="http://www.sparkfun.com/products/9570">MLX90614 IR Thermometer</a> - $20</li>
</ul>
<p>For a rough total of $112. You'll also need about $2 worth of resistors, which we'll get to shortly.</p>
<h2>First Things First</h2>
<p>You have to crawl before you can walk. To get started, I simply wanted to wire up the IR thermometer and do some testing. Full disclosure - I stood on the shoulders of giants throughout this project.</p>
<p>I followed a tutorial I found on <a href="http://bildr.org/2011/02/mlx90614-arduino/">bildr</a> to get the basic IR thermometer wiring (PS, bildr.org is awesome, and they do a great service to the world). From a hardware pespective, the IR thermometer has 4 pins: a ground, voltage in (V-in), a "data" pin, and a "clock" pin. See the diagram below from bildr:</p>
<ul>
<li>the V-in is the red wire, plugged into the 3.3V power supply on the Arduino.</li>
<li>the ground is the black wire, plugged into any of the "GND" connections on the Arduino</li>
<li>the blue line represents the "data" connection and should be connected to pin 4 on the Arduino</li>
<li>the green line represents the "clock" connection and should be connected to pin 5 on the Arduino</li>
</ul>
<p><a href="http://alexdglover.files.wordpress.com/2012/07/mlx90614_hookup.png"><img class="size-full wp-image-101" title="MLX90614 Wiring" src="{{ site.baseurl }}/assets/mlx90614_hookup.png" alt="" width="402" height="586" /></a> Courtesy of bildr.org</p>
<p>You'll also notice that the data and clock connections are connected to the V-in wire via 4.7 K Ohm resistors; these are called "<a href="http://en.wikipedia.org/wiki/Pull-up_resistor">pull up resistors</a>". Last, the good folks at bildr included a small capacitor between the V-in and ground, but the thermometer should work without it. You'll probably want to do all of these connections on your bread board, since we're prototyping and don't want to do any soldering yet. Check out my build here:</p>

<figure>
  <a href="http://alexdglover.files.wordpress.com/2012/07/imag0002.jpg"><img class="size-full wp-image-102" title="IR Thermometer on Bread Board" src="{{ site.baseurl }}/assets/imag0002.jpg" alt="" width="600" height="358" /></a>
  <figcaption><a href="http://alexdglover.files.wordpress.com/2012/07/imag0002.jpg">IR Thermometer on Bread Board</a></figcaption>
</figure>

<p>Notice the bands on the resistor - yellow, purple, red, gold. This is resistor shorthand. Yellow represents 40, purple/violet is 7, red is a multiplier of 100, and gold indicates the tolerance. So these resistors are (40+7)*100 Ohms, or 4700 Ohms, or 4.7K Ohms, with a +/- 5% tolerance. I tried to replicate the colors used in the bildr example for clarity.</p>
<p>Alright, the IR thermometer is all wired up and connected to Arduino, the hardware portion is complete. Download all the files, including the sketch and the I2C master library from the <a href="http://bildr.org/2011/02/mlx90614-arduino/">bildr post</a>. Don't forget to place the I2C master files in your Arduino libraries directory and restart your <a href="http://arduino.cc/hu/Main/Software">Arduino IDE</a>. Now for a quick test:</p>

{% include video id="46072409" provider="vimeo" %}

<p>Awesome! Basic temperature reading functionality established!</p>
<p>In Part 2, we'll incorporate the motor shield, the servo, and the chassis kit to finish the heat seeking vehicle.</p>
