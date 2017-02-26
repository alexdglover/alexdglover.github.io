---
title: Konami Code
date: 2012-11-16 07:30:38.000000000 -06:00
categories:
- IT/Software Projects
- How-to Guides
tags:
- easter egg
- javascript
- konami
---
<p>The Konami code... if you were born in 1990 or earlier, and play (or played) video games, you probably already know what I'm talking about.</p>
<p>The history of the Konami code is awesome in it's own rite. Here's the short version: the Konami code is a famous cheat code (up, up, down, down, left, right, left, right, B, A) that first appeared in NES games like Contra. Many times the code was added to video games as a tribute to the classic games or as a tribute to Kazuhisa Hashimoto, the developer who accidentally left the cheat code in the game when it was sold to the public.</p>
<p>That homage has been proliferated by nerds and techies through many games and, eventually, made it's way on websites.</p>
<p>YES, THERE IS A CHEAT CODE FOR THE INTERNET.</p>
<p>Sort of. It usually just reveals an easter egg on that site. You can find a whole list of websites that use Konami codes at Konami Code Sites. It's a lot of fun finding the secret content people have put behind respectable websites.</p>
<p>Some of you are probably thinking "But how do I put a Konami code on MY site?!?!" Well, at least that's what I was wondering.</p>
<p>There is a pretty robust Konami code javascript library that's even iPhone compatible, hosted at Google Code (on an iPhone, it's swiping and tapping, so it's "up, up, down, down, left, right, left, right, tap, tap, tap".</p>
<p>For those who are looking for the quickest and easiest version, I found this over at snaptortoise.com:</p>

```javascript
<script type="text/javascript" src="http://konami-js.googlecode.com/svn/trunk/konami.js"></script>
<script type="text/javascript">// <![CDATA[
konami = new Konami()
konami.load("http://www.google.com");
// ]]></script>
```

But that... is almost too easy. And you may want to do more custom things than just loading a different page. Or maybe you want to do a slightly different cheat code. I found some simpler, flexible (albeit not as flashy) javascript code on another blog:</p>

```javascript
// check to make sure that the browser can handle window.addEventListener
if (window.addEventListener) {
  // create the keys and konami variables
  var keys = [],
  konami = "38,38,40,40,37,39,37,39,66,65";
  // bind the keydown event to the Konami function
  window.addEventListener("keydown", function(e){
    // push the keycode to the 'keys' array
    keys.push(e.keyCode);
    // and check to see if the user has entered the Konami code
    if (keys.toString().indexOf(konami) >= 0) {
      // do something such as:
      alert('Konami');
      // and finally clean up the keys array
      keys = [];
    };
  }, true);
};
```

<p>The "38,38,40,40,37..." are keypress IDs, so you would change this portion if you wanted a different cheat code.</p>
<p>There's even a WordPress plugin for Konami codes!</p>
<p>Hope this was helpful, and enjoy the easter egg hunt!</p>
