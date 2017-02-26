---
title: 'Arduino Project 6: Unlock it with Fire'
date: 2015-07-12 15:10:09.000000000 -05:00
categories:
- Electronics Projects
- How-to Guides
tags:
- arduino
- door latch
- flame sensor
- lock box
- relay
header:
  teaser: /assets/UnlockItWithFireWiring.jpg
  overlay_image: /assets/IMG_20150328_144411.jpg
  overlay_filter: rgba(0, 0, 0, 0.2)
  overlay_color: "#5e616c"
  cta_label: "<i class='fa fa-github'></i> GitHub Repo"
  cta_url: "https://github.com/alexdglover/unlock-it-with-fire"
excerpt: A lock box that can only be opened with a different kind of key
---
<p>A couple years ago I built a <a href="http://alexdglover.com/arduino-project-4-tilt-to-unlock/" target="_blank">novelty lock box that used an Arduino and an accelerometer</a> so that you had to tilt the box in the correct sequence to unlock it. The prototype was pretty rough, so I eventually refined the design and built a Mark II version of it.</p>
<p>This project is my third iteration of the novelty lockbox. The majority of the design has remain unchanged, except for this project I used a "flame sensor" instead of an accelerometer. Instead of tilting the box in a particular sequence, the only way to open the box is to use an open flame as the "key." Here's a quick video to demonstrate the finished product:</p>

{% include video id="u50mTOLcKIY" provider="youtube" %}

<h3>Component List</h3>
<ul>
<li><a href="http://amzn.to/1LZYbvA" target="_blank">A 3-5.5v "flame sensor"</a></li>
<li><a href="http://amzn.to/1dUrDW0" target="_blank">An Arduino Uno</a> (or materials to build your own breakout board)</li>
<li>A DC power supply with both 5v and 12v output (I used an old <a href="http://amzn.to/1O3Z3OW" target="_blank">molex power supply</a>)</li>
<li>A <a href="http://amzn.to/1LZZLgT" target="_blank">single relay</a></li>
<li>A <a href="http://amzn.to/1O3ZfxI" target="_blank">12v door latch</a></li>
</ul>
<h3>Testing</h3>
<p>Before you do anything with the box, we want to test and calibrate the flame sensor. I followed <a href="http://www.instructables.com/id/Arduino-Modules-Flame-Sensor/?ALLSTEPS" target="_blank">this Instructable</a> to get acquainted with the flame sensor and what kind of values it returns. Note that the author is mapping the full range of values (0-1024) to just 4 values to match his sketch. Start by modifying this block:</p>

```c
void loop() {
  // read the sensor on analog A0:
  int sensorReading = analogRead(A0);
  // map the sensor range (four options):
  // ex: 'long int map(long int, long int, long int, long int, long int)'
  int range = map(sensorReading, sensorMin, sensorMax, 0, 3);
```

<p>by adding a line for logging the raw sensor reading, like this:</p>

```c
void loop() {
  // read the sensor on analog A0:
  int sensorReading = analogRead(A0);
  Serial.println(sensorReading);

  // map the sensor range (four options):
  // ex: 'long int map(long int, long int, long int, long int, long int)'
  int range = map(sensorReading, sensorMin, sensorMax, 0, 3);
```

<p>Now you can wire up the sensor, upload your sketch, and experiment with what kind of values you get with an open flame at different distances.</p>
<p>One quick clarification - I refer to this device as a "flame sensor" because that's how its marketed, but in reality it's just a photoresistor that has a filtering film around it that only allows infrared light to pass through. The voltage in the photoresistor circuit will change with the amount of light, and the Arduino analog pin will convert that voltage value to an integer between 0 and 1024. This means any infrared light will affect the sensor, including sunlight, TV remotes that use infrared, even something that is very hot could theoretically give off enough infrared to affect the sensor. If you're getting strange values while testing your sensor, be sure that you don't have another source of infrared throwing you off.</p>
<p>Once you're confident your sensor works and you have some idea of what values it returns, you're ready to build out the lock box components.</p>
<h3>Assembly</h3>
<p>Note: To save on cost, I've been building <a href="https://www.arduino.cc/en/Main/Standalone#.UwkAm_ldWuk">Arduino compatible breadboards</a> using standalone ATmega328 chips. The instructions will still work for an Arduino Uno, but the pictures won't be as helpful. Check out <a href="http://alexdglover.com/arduino-project-4-tilt-to-unlock/" target="_blank">Arduino Project 4: Tilt to Unlock</a> to see pictures for a similar assembly using an Arduino Uno.</p>
<ol>
<li>
  <p>Connect the 5v and ground pins (may be marked as '+' and 'G') on your flame sensor to the corresponding pins on the Arduino or bread board. Connect the A0 pin (the analog pin) on the flame sensor to the Analog Input 0 pin on the Arduino (green wire in the photo below, pin 23 on an ATmega328). Use <a href="http://amzn.to/1URGD8T" target="_blank">male-to-female jumper wires</a> to make this easier.</p>

  <p style="text-align: center;">
    <a href="{{ "/assets/IMG_20150328_110043-300x225.jpg" | absolute_url }}">
      <img class="aligncenter size-medium wp-image-979" src="{{ "/assets/IMG_20150328_110043-300x225.jpg" | absolute_url }}" alt="arduino-project-6-unlock-it-with-fire-wiring-1" width="300" height="225" />
    </a>
  </p>
</li>
<li>
  <p>Connect the 5v and ground pins (may be marked as 'VCC' and 'GND') on the relay to the corresponding pins on the Arduino or bread board. Connect the 'IN' pin to Digital Pin 8 (yellow wire in the photo above, pin 14 on an ATmega328). The digital pin will close the relay when voltage is applied, closing the circuit for our door latch and opening the lock. Use <a href="http://amzn.to/1URGD8T" target="_blank">male-to-female jumper wires</a> to make this easier.</p>
</li>
<li>
  <p>
    The door latch has two wires, they are arbitrary (either one can be powered or ground). Connect one of them to the ground wire from the power supply. Connect the other wire to the normally open ('NO') socket on the relay. Connect the 12v wire from the power supply to the always-closed or common socket on the relay (it's almost always the middle one). Here's a closeup of the wiring from <a href="http://alexdglover.com/arduino-project-4-tilt-to-unlock/" target="_blank">Arduino Project 4</a>:
  </p>

  <p style="text-align: center;">
    <a href="{{ "/assets/relay_wiring_example.jpg" | absolute_url }}">
      <img class="aligncenter size-medium wp-image-981" src="{{ "/assets/relay_wiring_example-300x225.jpg" |  absolute_url }}" alt="relay_wiring_example" width="300" height="225" />
    </a>
  </p>
  <p>
    So our project should be all wired up and ready for a smoke test. Go ahead and connect the power and make sure nothing starts smoking or getting too hot.
  </p>
  <p style="text-align: center;">
    <a href="{{ "/assets/UnlockItWithFireWiring.jpg" | absolute_url }}">
      <img class="aligncenter size-medium wp-image-980" src="{{ "/assets/UnlockItWithFireWiring.jpg" | absolute_url }}" alt="arduino-project-6-unlock-it-with-fire-wiring-2" width="300" height="225" />
     </a>
  </p>
</li>

<li>If you're still reading that means nothing melted or started smoking, which is great. At this point we're ready to upload a sketch and test. Go ahead and <a href="https://github.com/alexdglover/unlock-it-with-fire" target="_blank">download the sketch</a> and upload it to your Arduino. If all goes well, the door latch should open when you provide the infrared light. Here's a quick test video:


{% include video id="eS91XoLky7I" provider="youtube" %}


Now we're ready to assemble the box.</li>
<li>Drill a hole in the back of the cigar box just big enough to run your  power supply wires through, including the insulation. Run your wire through and let it hang outside of the box for later (this will also prevent you from locking yourself out of the box by accident).</li>
<li>
  <p>
    Remember how I mentioned other sources of infrared can interfere with your sensor? We can limit this effect by "tunneling" or narrowing the sensor's field of view. Start off by drilling a small hole in your cigar box. A 1/4" inch drillbit should work well for this. Make sure your hole placement lines up with a reasonable place to mount your flame sensor inside of the box. To be discreet, I drilled my hole on the bottom left of the "face" of the cigar box and mounted the sensor using double sided tape.
  </p>

  <p style="text-align: center;">
    <a href="{{ "/assets/IMG_20150328_113207.jpg" | absolute_url }}">
      <img class="aligncenter size-medium wp-image-983" src="{{ site.baseurl }}/assets/IMG_20150328_113207-225x300.jpg" alt="arduino-project-6-unlock-it-with-fire-flame-sensor-1" width="225" height="300" />
    </a>
  </p>
  <p style="text-align: center;">
    <a href="{{ "/assets/IMG_20150328_113223.jpg" | absolute_url }}">
      <img class="aligncenter size-medium wp-image-984" src="{{ site.baseurl }}/assets/IMG_20150328_113223-300x225.jpg" alt="arduino-project-6-unlock-it-with-fire-flame-sensor-2" width="300" height="225" />
    </a>
  </p>
</li>
<li>
  <p>
    If you're starting with a "clam-shell" style box (cigar boxes work great), you can follow my solution for the door latch. Secure the 12v door latch to the top of the cigar box with 1/4" screws. Next, take a <a href="http://amzn.to/1ShuHJl">sawtooth picture hangar</a> and nail it into place so it lines up with the 12v door latch. Obviously hammering inside of a cigar box can be difficult - you can also use a pair of pliers to squeeze the nail into place.
  </p>
  <p style="text-align: center;">
    <a href="{{ "/assets/IMG_20150328_113216.jpg" | absolute_url }}">
      <img class="aligncenter size-medium wp-image-985" src="{{ site.baseurl }}/assets/IMG_20150328_113216-300x225.jpg" alt="arduino-project-6-unlock-it-with-fire-latch-1" width="300" height="225" /></a>
  </p>
  <p style="text-align: center;">
    <a href="{{ "/assets/IMG_20150328_113232.jpg" | absolute_url }}">
      <img class="aligncenter size-medium wp-image-986" src="{{ site.baseurl }}/assets/IMG_20150328_113232-225x300.jpg" alt="arduino-project-6-unlock-it-with-fire-latch-2" width="225" height="300" />
    </a>
  </p>
</li>

<li>
  <p>
    At this point, things are going to start getting tight so go ahead and re-wire everything outside of the box. Once you're finished, mount your Arduino (or breadboard) and relay inside of the box. Try to keep all of these components mounted off to one side to save as much usable space as possible.
  </p>
  <p style="text-align: center;">
    <a href="{{ "/assets/IMG_20150328_144411.jpg" | absolute_url }}">
      <img class="aligncenter size-medium wp-image-987" src="{{ site.baseurl }}/assets/IMG_20150328_144411-300x225.jpg" alt="arduino-project-6-unlock-it-with-fire-mounting-components" width="300" height="225" />
    </a>
  </p>
  <p>I mounted all of my components to the side of the cigar box. Make sure your 12v door latch won't hit your other components when you close the box.
  </p>
</li>
</ol>
<p>That's it, your lock box is complete! As always I hope you enjoyed the post and I look forward to seeing what you build!</p>

[GitHub Repo](https://github.com/alexdglover/unlock-it-with-fire){: .btn .btn--info .btn--primary .btn--large }
{: .text-center}
