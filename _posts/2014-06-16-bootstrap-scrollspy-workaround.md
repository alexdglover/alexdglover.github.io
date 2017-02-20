---

title: Bootstrap Scrollspy Workaround
date: 2014-06-16 08:00:10.000000000 -05:00



categories:
- Fun IT Projects
- How-to Guides
tags:
- bootstrap
- javascript
- scrollspy
- web design
meta:
  _edit_last: '1'
  _s2mail: 'yes'
  _wpas_mess: "#Bootstrap Scrollspy Workaround http://wp.me/p3dG3x-cn"
  _wpas_done_all: '1'
  _wpas_skip_1477652: '1'
  _wpas_skip_1477650: '1'
---
<p>I was recently working on a new HTML project when I came across the <a title="Scrollspy" href="http://getbootstrap.com/2.3.2/javascript.html#scrollspy" target="_blank">Scrollspy</a> feature in Bootstrap's framework (v2.3). The feature looks really good on Bootstrap's pages, I figured I'd use it in my project as well. I immediately had issues with the links aligning the content to the viewport and issues with the scrollspy highlighting the right link based on what content was in the viewport. This was all supposed to be out of the box functionality... After doing some research (read: Googling), I came across a few <a href="https://github.com/twbs/bootstrap/issues/3316" target="_blank">bug tickets</a> and workarounds, but no wholistic solutions to reproducing the functionality on Bootstrap's site. So I decided to build it manually.</p>
<p>Let's get started.</p>
<p><!--more Continue to the instructions--></p>
<h2>Preparation</h2>
<p>If you want to jump right into the project, you can download <a href="http://experimental.alexdglover.com/downloads/BootstrapScrollspyWorkaroundStarter.zip" target="_blank">the starter kit</a> (it's just Boostrap 2.3, a modified Hero Unit example page, and a very slightly modified Bootstrap CSS).</p>
<p>If you want to start from a 100% clean version of Bootstrap 2.3, go <a href="http://getbootstrap.com/2.3.2/getting-started.html#download-bootstrap" target="_blank">download the source</a> and extract it. Keep your top-level folder name whatever you want it to be, but rename the 'bootstrap' folder assets (we are matching the existing folder structure in the source code we are going to borrow from one of their examples. Feel free to build your folder structure however you see fit). Create a sibling folder to contain your HTML files and create an index.html inside. Finally, go download <a href="http://jquery.com/download/" target="_blank">jQuery</a>, place it in your assets/js folder, and rename it 'jquery.js' (for simplicity). Your folder structure should look like this:</p>
```topLevelFolder
├── assets/
│   ├── css/
│   │   ├── bootstrap.css
│   │   ├── bootstrap.min.css
│   ├── js/
│   │   ├── bootstrap.js
│   │   ├── bootstrap.min.js
│   │   ├── jquery.js
│   └── img/
│       ├── glyphicons-halflings.png
│       └── glyphicons-halflings-white.png
├── html/
│   ├── index.html
```
<p>Perfect. Now browse to the <a href="http://getbootstrap.com/2.3.2/examples/hero.html" target="_blank">Hero example</a> and <span style="text-decoration: line-through;">steal</span> borrow their source code (just right click anywhere, view source, Ctrl+A Ctrl+C) and paste it into your index.html file. We have to make a few minor changes here as well. Scroll to the bottom of the code and replace this:</p>
```<!-- Le javascript
    ================================================== -->
    <!-- Placed at the end of the document so the pages load faster -->
    <script src="../assets/js/jquery.js"></script>
    <script src="../assets/js/bootstrap-transition.js"></script>
    <script src="../assets/js/bootstrap-alert.js"></script>
    <script src="../assets/js/bootstrap-modal.js"></script>
    <script src="../assets/js/bootstrap-dropdown.js"></script>
    <script src="../assets/js/bootstrap-scrollspy.js"></script>
    <script src="../assets/js/bootstrap-tab.js"></script>
    <script src="../assets/js/bootstrap-tooltip.js"></script>
    <script src="../assets/js/bootstrap-popover.js"></script>
    <script src="../assets/js/bootstrap-button.js"></script>
    <script src="../assets/js/bootstrap-collapse.js"></script>
    <script src="../assets/js/bootstrap-carousel.js"></script>
    <script src="../assets/js/bootstrap-typeahead.js"></script>```
<p>with this:</p>
```<!-- Le javascript
    ================================================== -->
    <!-- Placed at the end of the document so the pages load faster -->
    <script src="../assets/js/jquery.js"></script>
    <script src="../assets/js/bootstrap.js"></script>```
<p>For sanity testing, open index.html in your browser - it should look like the original and function without errors.</p>
<p>Now let's strip out the 3 sub-headings and replace them with a content area and a set of links in the sidebar.</p>
<p>Replace this:</p>
```
      <div class="row">
        <div class="span4">
          <h2>Heading</h2>
          <p>Donec id elit non mi porta gravida at eget metus. Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut fermentum massa justo sit amet risus. Etiam porta sem malesuada magna mollis euismod. Donec sed odio dui. </p>
          <p><a class="btn" href="#">View details &raquo;</a></p>
        </div>
        <div class="span4">
          <h2>Heading</h2>
          <p>Donec id elit non mi porta gravida at eget metus. Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut fermentum massa justo sit amet risus. Etiam porta sem malesuada magna mollis euismod. Donec sed odio dui. </p>
          <p><a class="btn" href="#">View details &raquo;</a></p>
       </div>
        <div class="span4">
          <h2>Heading</h2>
          <p>Donec sed odio dui. Cras justo odio, dapibus ac facilisis in, egestas eget quam. Vestibulum id ligula porta felis euismod semper. Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut fermentum massa justo sit amet risus.</p>
          <p><a class="btn" href="#">View details &raquo;</a></p>
        </div>
      </div>```
<p>with this:</p>
```
      <div class="row">
		  <div class="span3 sidebar">
		    <ul class="nav nav-list nav-tabs nav-stacked sidebarNav affix-top" style="top:80px; width:255px;">
			  <li id="section1Nav"><a href="#section1">Section 1<i class="icon-chevron-right" style="float:right;"></i></a></li>
			  <li id="section2Nav"><a href="#section2">Section 2<i class="icon-chevron-right" style="float:right;"></i></a></li>
			  <li id="section3Nav"><a href="#section3">Section 3<i class="icon-chevron-right" style="float:right;"></i></a></li>
			  <li id="section4Nav"><a href="#section4">Section 4<i class="icon-chevron-right" style="float:right;"></i></a></li>
			</ul>
		  </div>

		  <div class="span9">
			<section id="section1">
				<div class="page-header">
					<h1>section 1</h1>
				</div>
				<h2>Sub Header</h2>
				<p>lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet.</p>
				<p>lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet.</p>
				<p>lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet.</p>
				<p>lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet.</p>
			</section>
			<section id="section2">
				<div class="page-header">
					<h1>section 2</h1>
				</div>
				<h2>Sub Header</h2>
				<p>lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet.</p>
				<p>lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet.</p>
			</section>
			<section id="section3">
				<div class="page-header">
					<h1>section 3</h1>
				</div>
				<h2>Sub Header</h2>
				<p>lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet.</p>
				<p>lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet.</p>
				<p>lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet.</p>
			</section>
			<section id="section4">
				<div class="page-header">
					<h1>section 4</h1>
				</div>
				<h2>Sub Header</h2>
				<p>lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet.</p>
				<p>lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet.</p>
				<p>lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet lorem ipsum dolor sit amet.</p>
			</section>
		  </div>
      </div>```
<p>Open this up in your browser and verify everything is kosher. If so, we're all done with prep and can move on to the custom JavaScript.</p>
<h2>The Offset Logic</h2>
<p>At this point, you probably clicked the links and noticed that the viewport doesn't line up quite perfectly. This is because the height of the top nav bar isn't being taken into account. Let's fix that first, then reproduce the scrollspy effect.</p>
<p>To fix this issue, we need to 'intercept' the normal link function and replace it with our own. Once intercepted, we'll scroll to that element with an offset equal to the height of the header.</p>
```
    // Here we set the offset value to whatever the header height is
    var offset = 60;
    // If a user clicks on any anchor element (a) within a list item tag (li) on our sidebar with the class sidebarNav (.sidebarNav), execute this function
    $('.sidebarNav li a').click(function(event) {
        // Prevent the normal scroll behavior
        event.preventDefault();
        // Grab the clicked element's href tag, scroll that element into view
        $($(this).attr('href'))[0].scrollIntoView();
        // Scroll 0 pixels horizontally and -60 (really negative offset) vertically
        scrollBy(0, -offset);			
    });
</script>```
<p>That's it. Add this block of javascript in your index.html just after your script source declaration for jQuery and boostrap.js. Test it in your browser, and experiment with changing the offset number and its effects.</p>
<h2>The Scrollspy Effect</h2>
<p>To create the Scrollspy effect, all we need to do is add the 'active' class to the li tags when the viewport is lined up with the corresponding section.</p>
```
$(document).ready(function () {
	var menu = $('.sidebarNav');

	$(window).scroll(function () {
		var currPosition = $(this).scrollTop();
		var section1Position = $('#section1').offset().top - 100;
		var section2Position = $('#section2').offset().top - 100;
		var section3Position = $('#section3').offset().top - 100;
		// The offset for section 4 is larger because it is at the bottom of the page,
		// and if the offset was only 100, the window position would never 'enter' the
		// section's field
		var section4Position = $('#section4').offset().top - 150;

		// If the window is at or below the section 1 tag, then switch the 
		// sidebar's class from affix-top to affix so it will move with the window
		if (currPosition >= section1Position) {
			menu.removeClass('affix-top').addClass('affix');

		}
		// If the window is NOT at or below the section 1 tag, then switch the 
		// sidebar's class from affix to affix-top so it will position itself
		// below the Hero Unit
		else{
			menu.removeClass('affix').addClass('affix-top');
		}

		// If the window is at or below the section 1 tag, then add the active class
		// to the corresponding li tag and remove the active class from all other li 
		// tags in the side bar. Apply the same logic to all of the other sections
		if (currPosition >= section1Position && currPosition <= section2Position)
		{
			$('#section1Nav').addClass('active').siblings().removeClass('active');
		}
		else if(currPosition >= section2Position && currPosition <= section3Position)
		{
			$('#section2Nav').addClass('active').siblings().removeClass('active');
		}
		else if(currPosition >= section3Position && currPosition <= section4Position)
		{
			$('#section3Nav').addClass('active').siblings().removeClass('active');
		}
		else if(currPosition >= section4Position)
		{
			$('#section4Nav').addClass('active').siblings().removeClass('active');
		}
	});
});
</script>```
<p>Again, you'll need to add this code block after the script source declarations for jQuery boostrap.js. That's it. Now you've got a dead-accurate version of the scrollspy, with complete control. Demo and source for the complete package are below. </p>
<p style="text-align: center;"><a href="http://experimental.alexdglover.com/BootstrapScrollSpyWorkaround/html/index.html"><input class="button" type="button" value="Demo" /></a> <a href="http://experimental.alexdglover.com/downloads/BootstrapScrollspyWorkaroundFinal.zip"><input class="button" type="button" value="Source" /></a></p>
<p>Enjoy.</p>
