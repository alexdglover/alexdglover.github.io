---

title: 'Arduino Project 2: The Audible Eye Part 1'
date: 2013-01-07 08:35:16.000000000 -06:00



categories:
- Fun Electronics Projects
- How-to Guides
tags:
- arduino
- audio output
- ultrasonic rangefinder
meta:
  _wpas_done_all: '1'
  _publicize_pending: '1'
  jabber_published: '1357575671'
  reddit: a:2:{s:5:"count";i:0;s:4:"time";i:1357695021;}
  publicize_twitter_user: alexdglover
  _wpas_done_1477652: '1'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:297624509;b:1;}}
  _wpas_done_1477650: '1'
  email_notification: '1357575674'
  _wpas_skip_1477652: '1'
  _wpas_skip_1477650: '1'
---
<p>The Audible Eye is a short proof-of-concept project that I came up with while looking for ways to experiment with some new gear. The idea of combining an ultrasonic rangefinder and some sort of signaling audio output jumped out to me - it would give you a depth perception, not unlike echo location that bats and dolphins use. As far as practical use, I believe it could be used by the visually impaired as a complement to a <a href="http://en.wikipedia.org/wiki/White_cane" target="_blank">white cane</a>, but not as a replacement.</p>
<p>Basically, the tone/pitch of the audio signal would get higher as the operator moves closer to a wall or object, indicating to the operator that they are getting closer. Similarly, as the operator moves away from the wall the tone will drop until it's almost inaudible.</p>
<p>Let me provide some context to make this more clear. Let's imagine our operator is blindfolded. If the operator pointed the device down an empty hallway for example, they would hear almost no tone, telling them it's safe to walk forward. As they approached a wall, the tone would increase. The operator would then scan around them with the device, find another path that was unobstructed, and could continue walking.</p>
<p>This video is all but unwatchable (quality is IMPRESSIVELY<i> </i>bad), but it will at least give you an idea of how it works. And of course I disassembled the project before I realized the video was botched.</p>

{% include video id="56793658" provider="vimeo" %}

<p>Alright, let's open up the enclosure and start breaking down the project. The entire project can be broken down into just a few components. Here's a view inside of the enclosure.</p>
<p><a href="http://alexdglover.files.wordpress.com/2013/01/imag0361.jpg"><img class="aligncenter size-full wp-image-312" alt="alexdglover_audible_eye" src="{{ site.baseurl }}/assets/imag0361.jpg" width="595" height="355" /></a></p>
<p>Component List:</p>
<ul>
<li><a href="http://rcm.amazon.com/e/cm?lt1=_blank&amp;bc1=000000&amp;IS2=1&amp;bg1=FFFFFF&amp;fc1=000000&amp;lc1=0000FF&amp;t=alexdgloverwo-20&amp;o=1&amp;p=8&amp;l=as4&amp;m=amazon&amp;f=ifr&amp;ref=ss_til&amp;asins=B006H06TVG" target="_blank"><span style="line-height:13px;">Arduino Uno</span></a></li>
<li><a href="http://www.amazon.com/gp/product/B004U8TOE6/ref=as_li_ss_tl?ie=UTF8&amp;tag=alexdgloverwo-20&amp;linkCode=as2&amp;camp=1789&amp;creative=390957&amp;creativeASIN=B004U8TOE6" target="_blank">HC-SR04 Ultrasonic Rangefinder</a></li>
<li><a href="http://www.amazon.com/gp/product/B004RXKWDQ/ref=as_li_ss_tl?ie=UTF8&amp;tag=alexdgloverwo-20&amp;linkCode=as2&amp;camp=1789&amp;creative=390957&amp;creativeASIN=B004RXKWDQ" target="_blank">Breadboard and jumper wires</a></li>
<li><a href="http://www.amazon.com/gp/product/B005D65LEG/ref=as_li_ss_tl?ie=UTF8&amp;tag=alexdgloverwo-20&amp;linkCode=as2&amp;camp=1789&amp;creative=390957&amp;creativeASIN=B005D65LEG" target="_blank">9v Battery Adapter</a></li>
<li><a href="http://www.amazon.com/gp/product/B0078ZAVZO/ref=as_li_ss_tl?ie=UTF8&amp;tag=alexdgloverwo-20&amp;linkCode=as2&amp;camp=1789&amp;creative=390957&amp;creativeASIN=B0078ZAVZO" target="_blank">3 Pin Toggle Switch</a></li>
<li>9v Battery (stop putting it on your tongue)</li>
<li>Headphones (or any other audio output device)</li>
</ul>
<p>Building the project can be broken down into phases. Let's focus on the ultrasonic rangefinder first, and build from there.</p>
<h3>Setting up the Ultrasonic Rangefinder</h3>
<p>There are 4 pins on the HC-SR04 ultrasonic sensor module: voltage in (labeled "Vcc" on my module), ground, TRIG, and ECHO. The TRIG pin will control the transmission of ultrasound. The ECHO pin will be the receiver pin. Use your jumper wires to connect the ground on the module to the ground on the Arduino (red wire near the top of the above picture) and connect the voltage in pin to the +5v pin on the Arduion (white wire near the top of the above picture). Next, use jumper wires to connect the TRIG and ECHO pins to two of the digital pins on the Arduino. These will be the numbered pins on the Arduino that do NOT have the tilde (~) prefix. In my build, I used pin 2 for the TRIG connection and pin 7 for the ECHO connection. Here's a better picture:</p>
<p><a href="http://alexdglover.files.wordpress.com/2013/01/imag0372.jpg"><img class="aligncenter size-full wp-image-314" alt="alexdglover_audible_eye_arduino_closeup" src="{{ site.baseurl }}/assets/imag0372.jpg" width="595" height="994" /></a></p>
<p>OK we're all wired up! There are several libraries for interacting with ultrasonic rangefinders. The one I used is probably out of date, but you can <a href="http://arduino-info.wikispaces.com/file/view/Ultrasonic.zip/242151837/Ultrasonic.zip" target="_blank">download it here</a>.</p>
<p>Note: If you're having issues with this library, edit Ultrasonic.cpp and Ultrasonic.h. Replace this line:<br />
#include "WProgram.h"</p>
<p>with this:<br />
#include "Arduino.h"</p>
<p>Once you've downloaded it, add the entire directory (should contain Ultrasonic.h, Ultrasonic.cpp, keywords.txt, and an examples directory) to your Arduino's libraries directory. I'm being lazy and have the Arduino install on my desktop, so my library directory is here:</p>

`C:\Users\Alex\Desktop\arduino-1.0.1\libraries\`

<p>Once that's done, open up the Arduino IDE. Click on the "Sketch" dropdown menu, click "Import Library" and verify that Ultrasonic is a listed library. If it's there, you're in good shape.</p>
<p>Visit <a href="http://arduino-info.wikispaces.com/UltraSonicDistance" target="_blank">http://arduino-info.wikispaces.com/UltraSonicDistance</a> and check out the Arduino Sketch provided there. Edit the TRIG_PIN and ECHO_PIN variables to coincide with the pins you used (remember I used 2 and 7 in my case). Connect your Arduino via USB and upload the Sketch. Finally, open the serial monitor (Tools dropdown --> Serial Monitor). You should see readings in inches and centimeters being reported back to the serial monitor.</p>
<p>Awesome - this is our first building block.</p>
<h3>Setting up the Audio Output</h3>
<p>This step is super easy, unless you're me and you waste an hour getting stereo wires confused. If you're using a piezo-electric piece or a simple mono-speaker or headphones, there should be only 2 wires - a voltage in and a ground. Simple. Now, the scrap headphones I had were <i>stereo </i>headphones, which threw me off. If you have a stereo device, you'll likely have 2 separate insulated sections with 2 types of wiring each. In each insulated wire is one plain copper wire and one painted wire. The copper wires are both grounds and the painted wires are the right and left voltage-in connections. Since we're just doing simple mono output, go ahead and twist the two copper wires together and twist the two painted wires together. Next, connect the copper ground wires to one of the grounds on the Arduino. For the voltage-in wires, connect them to one of the PWM pins on the Arduino (I used pin 11). Here's a closeup of the headphone wiring:</p>
<p><a href="http://alexdglover.files.wordpress.com/2013/01/imag0374.jpg"><img class="aligncenter size-full wp-image-317" alt="alexdglover_audible_eye_headphone_connections" src="{{ site.baseurl }}/assets/imag0374.jpg" width="595" height="355" /></a></p>
<p>Now to test! Conveniently, the Arduino IDE (at least version 1.01) includes a few audio output sketches. Go to File --> Examples -->2. Digial --> Tone Melody. Search the Sketch for the tone() function - change the pin number in each of these functions to coincide with the pin you connected your headphones/speaker to. Now upload the Sketch to your Arduino - if everything is working, you should hear some the melody of "Shave and a haircut, two bits."</p>
<p>Awesome - now we have two of our building blocks completed.</p>
<p>Cliffhanger! In <a title="Arduino Project 2: The Audible Eye Part 2" href="{{ site.baseurl }}/arduino-project-2-the-audible-eye-part-2/">part 2</a>, we'll wire up the toggle switch and battery, as well as write the code to combine the rangefinder and the headphones to complete "The Audible Eye."</p>
