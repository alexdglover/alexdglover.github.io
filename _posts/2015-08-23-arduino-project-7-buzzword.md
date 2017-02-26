---
title: 'Arduino Project 7: Buzzword'
date: 2015-08-23 13:13:04.000000000 -05:00
categories:
- Electronics Projects
- How-to Guides
tags:
- arduino
- games
- tft
- touchscreen
header:
  teaser: /assets/IMG_20150704_103609-300x225.jpg
  overlay_image: /assets/arduino-closeup.jpg
  overlay_color: "#5e616c"
  cta_label: "<i class='fa fa-github'></i> GitHub Repo"
  cta_url: "https://github.com/alexdglover/buzzword"
  caption: Image courtesy of <a href="https://commons.wikimedia.org/wiki/File:Arduino_crystal-1.jpg">Wikimedia Commons</a>
excerpt: A "Catch Phrase" clone I built with an Arduino and TFT Touch Screen
---
<p>Here's a quick and easy project you can do with minimal hardware components. I call this project/device "Buzzword" but it's basically the same game and concept as <a href="http://amzn.to/1LKt0o1" target="_blank">Catch Phrase</a>. In Catch Phrase, you have a device that shows you random words. You can say anything to help your team guess the word as long as you don't say any part of the "catch phrase" itself, say "it rhymes with <blank>," or otherwise cheat. Once your team correctly guesses the word, you pass the device to the opposing team. This goes on until you run out of time. Whichever team is holding the device when time is up loses.</p>
<p>My game is very similar, except teams compete by seeing how many points they can get rather than simply whoever is holding the device at the end of the round. In addition, since you are providing your own list of words for the game in the code, you can update it once you get bored with the words or add new pop culture references.</p>
<p>Here's a quick demo:</p>

{% include video id="9fIOSbhoIIk" provider="youtube" %}

<p>Or for those who don't want to wait for a video to load, here are a few screenshots that should give you an idea of how it works.</p>
<ol>
<li>Here's the initial startup screen<br />
<a href="http://alexdglover.com/wp-content/uploads/2015/07/IMG_20150704_103515.jpg"><img class="aligncenter size-medium wp-image-970" src="{{ site.baseurl }}/assets/IMG_20150704_103515-300x225.jpg" alt="buzzword demo screenshot 1" width="300" height="225" /></a></li>
<li>Once you click the button to start, a 60 second timer starts and you're shown the first word. The list of words is shuffled every time the game starts. At this point, if you're holding the device, you can pass if it's too hard, or start throwing out clues. If someone guesses correctly, you hit the +1 button which will increment your team's score and load up the next buzzword for the next player<br />
<a href="http://alexdglover.com/wp-content/uploads/2015/07/IMG_20150704_103530.jpg"><img class="aligncenter size-medium wp-image-971" src="{{ site.baseurl }}/assets/IMG_20150704_103530-300x225.jpg" alt="buzzword demo screenshot 2" width="300" height="225" /></a></li>
<li>This continues...<br />
<a href="http://alexdglover.com/wp-content/uploads/2015/07/IMG_20150704_103609.jpg"><img class="aligncenter size-medium wp-image-969" src="{{ site.baseurl }}/assets/IMG_20150704_103609-300x225.jpg" alt="buzzword demo screenshot 3" width="300" height="225" /></a></li>
<li>Until finally you run out of time and you get your final score<br />
<a href="http://alexdglover.com/wp-content/uploads/2015/07/IMG_20150704_103624.jpg"><img class="aligncenter size-medium wp-image-972" src="{{ site.baseurl }}/assets/IMG_20150704_103624-300x225.jpg" alt="buzzword demo screenshot 4" width="300" height="225" /></a></li>
</ol>
<h2>Components Needed</h2>
<ul>
<li><a href="http://amzn.to/1ChvsAp" target="_blank">Arduino Uno</a></li>
<li><a href="http://amzn.to/1NGEhod" target="_blank">Arduino Uno compatible 2.8" TFT Touch Shield V2.0 from Seeedstudio</a> (you could use other boards but may need different libraries)</li>
<li><a href="http://amzn.to/1CasgpR" target="_blank">9v Battery Power Pack with DC male barrel connection</a></li>
</ul>
<h2>Construction/Assembly</h2>
<p>This one's going to be really simple. The trickiest part is going to be loading the libraries before you upload your sketch.</p>
<ol>
<li>Connect the TFT Touch Shield to the Arduino. It only goes on one way, so you can't really mess this up. If it doesn't slip into place immediately, check that the pins are lining up and bend them as necessary. If all of the pins line up well, don't be afraid to use a little force.</li>
<li>Download the relevant libraries and my sketch
<ol>
<li><a href="https://github.com/Seeed-Studio/Touch_Screen_Driver" target="_blank">Seeedstudio Touchscreen Library</a></li>
<li><a href="https://github.com/alexdglover/buzzword" target="_blank">The Buzzword Sketch (includes additional libraries)</a></li>
</ol>
</li>
<li>Connect the Arduino Uno to your computer with the USB cable.</li>
<li>Open the Arduino IDE. If you don't have it already, <a href="https://www.arduino.cc/en/Main/Software" target="_blank">download it</a> and install before proceeding.</li>
<li>Add the Seeed-Studio Touch_Screen_Driver and the TFTv2_Touch_Shield-Text_Direction libraries
<ol>
<li>In the Arduino IDE menu, go to Sketch --> Import Library... --> Add Library...</li>
<li>Find the Touch_Screen_Driver-master library you downloaded earlier in the file system browser and select it. The directory should contain SeeedTouchScreen.cpp file, a SeeedTouchScreen.h file, and a keywords.txt file at a minimum.</li>
<li>Repeat the above steps for the TFTv2_Touch_Sheild-Text_Direction library</li>
</ol>
</li>
<li>Open the Buzzword sketch and the font.c file
<ol>
<li>In the Arduino IDE menu, go to File --> Open...</li>
<li>Find the Buzzword.ino file in the Buzzword directory and select it. The main editor window in the Arduino IDE should now be populated with the code</li>
<li>In the Arduino IDE menu, go to Sketch --> Add File...</li>
<li>Find the font.c in the root of the Buzzword directory and select it. A second tab should pop up and be populated with some text.</li>
</ol>
</li>
<li>Compile the code to verify
<ol>
<li>Click on the buzzword tab in the Arduino IDE</li>
<li>Click the icon in the top left with a checkmark symbol - this will compile the code and verify there are no compilation issues.</li>
</ol>
</li>
<li>If all is well at this point, upload to your Arduino by clicking the icon in the top left with a right arrow icon. This will re-compile the code and attempt to write it to the Arduino itself. The screen should light up and display the start button, and should be ready to rock at this point.</li>
<li>If the device is working as expected, go ahead and disconnect the USB, plug in the battery pack, and secure the battery pack (two-sided tape or zip ties are good temporary solutions).</li>
<li>Play Buzzword!</li>
</ol>
