---

title: New Site Launch + 6 WordPress.com Migration Tips
date: 2013-03-09 21:50:09.000000000 -06:00



categories:
- How-to Guides
tags:
- easy adsense
- jetpack
- migration
- subscribe2
- wordpress
- wordpress.com
meta:
  _s2mail: 'yes'
  _edit_last: '1'
  _wpas_done_all: '1'
---
<p>Like all good things, my time on WordPress.com's hosting has come to an end. Don't get me wrong - anyone toying with the idea for a blog should strongly consider it, as it's well-managed and does 90% of what any user needs it to do. It's a powerful tool for the uninitiated...</p>
<p><a href="http://alexdglover.com/wp-content/uploads/2013/03/bane.png"><img class="aligncenter size-full wp-image-502" alt="bane-but-we-are-initiated" src="{{ site.baseurl }}/assets/bane.png" width="460" height="663" /></a></p>
<p>So as one of the 'initiated,' I had to go to my own hosting to take full control. For subscribers of my old site, you've been migrated (this is probably obvious since this post has been emailed to you) with the rest of the site. If you're just visiting the site, consider <a title="Register" href="http://alexdglover.com/membership-account-2/">registering</a>  - it's free, hides all advertisements, and signs you up to receive future posts by email.</p>
<p>Now, in the process of migrating to my own WordPress instance, I've learned a number of valuable things. And what's a better way to transition to a new site than to cover the lessons learned from the migration? I present to you, the 6 WordPress.com Migration Tips!</p>
<p><!--more--></p>
<h1>6 WordPress.com Migration Tips</h1>
<p>Now some of these tips are more generic, some are specific to migrating FROM WordPress.com hosting. I'm not going to cover the hosting setup or WordPress install, but leave a comment if you have questions related to those processes.</p>
<ol>
<li><span style="line-height: 13px;"><span style="line-height: 13px;"><span style="line-height: 13px;">Assuming you want to keep all of your pages/posts/images, your first step should be to do an export/import. On your WordPress.com site's dashboard, go to Tools --&gt; Export</span></span></span><a style="line-height: 13px;" href="http://alexdglover.com/wp-content/uploads/2013/03/wp_export.png"><img class="aligncenter size-full wp-image-505" alt="WordPress export link" src="{{ site.baseurl }}/assets/wp_export.png" width="364" height="173" /></a>WordPress.com will present  you with 2 options, a free XML export or a "Guided Transfer" for a whopping $129... just do the XML export. Remember where you saved your XML export! Next, go to your new WordPress instance dashboard  and choose Tools --&gt; Import. Click on the WordPress option, and install the necessary plugin. Then upload your XML file. The import should include all pages, posts, and even images/media. Now, that's all pretty useless, "WordPress-101" information. Here's the important tip - if you do the import, and you seem to be missing pages/posts, try 2 things:i. Re-run the import - the WordPress import function is smart enough to skip any posts/pages that already exist based on permalink.<br />
ii. Go to your "All Posts" view and look for "Deleted" posts. A good number of my posts were marked as deleted for some reason. Then just select all of the posts that should NOT be deleted, and click restore!<br />
<span style="line-height: 13px;"><br />
</span></li>
<li>Like the stats, WP Reader, and "Freshly Pressed" features that WordPress.com offers? Not to fear - install <a title="JetPack" href="http://wordpress.org/extend/plugins/jetpack/" target="_blank">JetPack</a>! JetPack allows you to connect your stand-alone instance of WordPress back to the WordPress.com's admin panel, allowing you to continue using all of the functionality you've come to know and love. I was pretty psyched that I was still  able to use the "Views by Country" feature after going to my own WP instance.<br />
<a href="http://alexdglover.com/wp-content/uploads/2013/03/wp_stats.png"><img class="aligncenter  wp-image-510" alt="wordpress_stats" src="{{ site.baseurl }}/assets/wp_stats.png" width="452" height="242" /></a></li>
<li>Set up a subscription service. JetPack offers a form for readers to subscribe to your blog like when it was hosted at WordPress.com. Alternatively, if you're looking for some additional control and features (or if you don't want to rely on WordPress.com), check out <a title="Subscribe2" href="http://wordpress.org/extend/plugins/subscribe2/" target="_blank">Subscribe2</a>. It's a no muss, no fuss plugin that allows users to subscribe to your blog with their email address. Unlike the JetPack service, you can control which posts do and do not get pushed out to your subscribers. Any user that is registered with your instance will also receive post notifications, which can come in handy if you're using a membership plugin.</li>
<li>If you're changing themes, try to decide on one before you spend time customizing widgets! Oftentimes when you activate a new theme, your widgets will get deactivated. If you do change themes after customizing, don't re-create all of the widgets - they should be in the Inactive Widget area at the bottom of the widgets screen! I made the mistake of re-creating everything. The widget settings should still be saved, you just need to drag them into the sidebar to reactivate them.<br />
<a href="http://alexdglover.com/wp-content/uploads/2013/03/wp_widget1.png"><img class="aligncenter  wp-image-513" alt="WP Inactive Widget Area" src="{{ site.baseurl }}/assets/wp_widget1.png" width="609" height="117" /></a></li>
<li>If you've left WordPress.com so that you can start advertising on your blog, I strongly suggest you check out <a title="Easy AdSense" href="http://wordpress.org/extend/plugins/easy-adsense-lite/" target="_blank">Easy AdSense</a>. It allows you to easily inject Google ads into posts, sidebars, and headers/footers. Easy AdSense also attempts to limit the number of ads on any given page to comply with Google's policies. Best of all, it's free!</li>
<li>This is a very broad statement, but start thinking more like a sysadmin - you're no longer operating in the safe sandbox of WordPress.com. When you're on your own hosting, you're fully capable of deleting your entire instance and all of your content (or otherwise royally screwing things up). Start taking backups, make sure your domain name doesn't expire or get sold to another party, and keep your instance and plug-ins updated.</li>
</ol>
<p>I hope some of these tips help you with your migration, and I hope you enjoy the new site.</p>
