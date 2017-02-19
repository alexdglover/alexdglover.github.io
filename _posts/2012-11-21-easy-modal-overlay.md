---

title: Easy Modal Overlays
date: 2012-11-21 08:00:12.000000000 -06:00



categories:
- Fun IT Projects
- How-to Guides
tags:
- jQuery
- modal overlay
- web design
meta:
  _wpas_done_all: '1'
  _publicize_pending: '1'
  jabber_published: '1353506886'
  publicize_twitter_user: alexdglover
  _wpas_done_1477652: '1'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:297624509;b:1;}}
  email_notification: '1353506889'
  _wpas_done_1477650: '1'
  _wpas_skip_1477652: '1'
  _wpas_skip_1477650: '1'
  tagazine-media: a:7:{s:7:"primary";s:70:"http://alexdglover.files.wordpress.com/2012/11/modaloverlayexample.png";s:6:"images";a:1:{s:70:"http://alexdglover.files.wordpress.com/2012/11/modaloverlayexample.png";a:6:{s:8:"file_url";s:70:"http://alexdglover.files.wordpress.com/2012/11/modaloverlayexample.png";s:5:"width";i:725;s:6:"height";i:535;s:4:"type";s:5:"image";s:4:"area";i:387875;s:9:"file_path";b:0;}}s:6:"videos";a:0:{}s:11:"image_count";i:1;s:6:"author";s:8:"34836694";s:7:"blog_id";s:8:"34954019";s:9:"mod_stamp";s:19:"2012-11-21
    15:10:21";}
  _edit_last: '1'
  _s2mail: 'yes'
---
<p>Modal overlays are those fancy popups, often used to display a full-sized image or some sort of form. They give the appearance of a new window within the browser, giving sites a more lively animated and interactive look.</p>
<p><a href="http://alexdglover.files.wordpress.com/2012/11/modaloverlayexample.png"><img class="aligncenter size-medium wp-image-234" title="modalOverlayExample" alt="" src="{{ site.baseurl }}/assets/modaloverlayexample.png?w=300" width="300" height="221" /></a></p>
<p>It's a pretty sweet feature, and is actually pretty easy to implement. I'll highlight a couple options and talk about how to implement them in this post.</p>
<h3>Lightbox</h3>
<p><a href="http://lokeshdhakar.com/projects/lightbox2/" target="_blank">Lightbox</a> is a well known modal overlay package that uses jQuery. The implementation of Lightbox is very straightforward</p>

1. Download Lightbox files</li>
2. Move the files into their respective folders where your site is hosted(e.g. move all of the Lightbox CSS files to the directory where you host all your CSS files)</li>
3. Add script references to the head of your HTML document in the right order:

   ```javascript
   <script src="js/lightbox.js"></script>
   <link href="css/lightbox.css" rel="stylesheet" />
   ```


4. Make sure that the path to the Lightbox images is correct in lightbox.css and screen.css files, otherwise the images won't render</li>
5. Now for the best part - add your images using an anchor tag, like this:

   ```html
   <a href="img/image1.jpg" rel="lightbox" title="some caption">some arbitrary text</a>
   ```

<p>That's it! The CSS and Javascript take care of the rest! Each image can have a title (just modify the title attribute). You can also have a gallery of images, by using the same group name in each rel attribute, like this:</p>

```html
<a href="img/image2.jpg" rel="lightbox[group_name]">Image 2 Name</a>
<a href="img/image3.jpg" rel="lightbox[group_name]">Image 3 Name</a>
```

<p><span style="color: #555555; font-size: 16px; font-weight: bold; line-height: 24px;">Custom (but easy) Modal Overlay with jQuery Tools</span></p>
<p>If you want to do something a little different than Lightbox, check out the <a href="http://jquerytools.org/demos/overlay/index.html" target="_blank">jQuery Tools Overlay</a>. Also built using jQuery, the jQuery Tools approach is a little less prescribed, a little more open.</p>
<p>Here's the step-by-step approach:</p>

1. Add images to your web site or web app with img tags, giving an arbitrary value to the rel attribute with a # sign like this:

   ```html
   <img src="img/image2.jpg" rel="#arbitrary2"/>
   ```

2. Create the content you want to appear in the modal overlay within a div tag. Assign the same arbitrary value to the id attribute, and give the div a class name that you will create in the CSS like this:

   ```html
   <div class="arbitrary_class_name" id="arbitrary1">...</div>
   ```

3. Define the "arbitrary_class_name" in your CSS file. I re-used most of the CSS on the jQuery Tools' tutorial:

   ```css
   .arbitrary_class_name
   {
   
   /* must be initially hidden */
   display:none;
   
   /* place overlay on top of other elements */
   z-index:10000;
   
   /* styling */
   background-color:#333;
   
   width:675px;
   min-height:200px;
   border:1px solid #666;
   
   /* CSS3 styling for latest browsers */
   -moz-box-shadow:0 0 90px 5px #000;
   -webkit-box-shadow: 0 0 90px #000;
   }
   /* close button positioned on upper right corner */
   .
   arbitrary_class_name .close {
   background-image:url(img/close.png);
   position:absolute;
   right:-15px;
   top:-15px;
   cursor:pointer;
   height:35px;
   width:35px;
   }
   ```

4. Don't forget to add a reference to your CSS file in the head of the HTML file, as well as a reference to the jQuery Tools javascript:

   ```html
   <link href="css/yourStyleSheet.css" rel="stylesheet" />
   ```

5. Last step - add this short Javascript to the your HTML document:

   ```javascript
   $(document).ready(function() {
     $("img[rel]").overlay();
     });
   </script>
   ```

<p>That's it!</p> 
