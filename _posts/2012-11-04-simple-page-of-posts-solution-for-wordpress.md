---
title: Simple Page-of-Posts Solution for Wordpress
date: 2012-11-04 08:44:47.000000000 -06:00
categories:
- How-to Guides
- Utilities And Other Useful Things
tags:
- page of posts
- wordpress
header:
  teaser: /assets/page_of_posts_screenshot.png
---
**Be advised** This post is quite old ({{ page.date | date_to_string }}) and any code may be out of date. Proceed with caution.
{: .notice--warning}

<p>Recently I have been helping Make a Difference Wisconsin, a local non-profit, with their Wordpress website. Without going into details as to why,  I needed to create a simple 'page of posts' for menu hierarchy reasons. It needed to be a page, but I wanted it to display all of the posts from a particular category. Google took me to many solutions, including the <a href="http://codex.wordpress.org/Pages#A_Page_of_Posts" target="_blank">Wordpress Codex</a>, but none of them worked. Some used out of date or deprecated query functions and some didn't work with the custom theme that was already in place. This should have been a very easy task, but took me several  days to pin down my solution.</p>
<p>So, if you want to create a simple page-of-posts that should work in any theme (with some minor tweaking, of course), feel free to use this code:</p>


```php
<?php
/*
Template Name: pageofposts
*/
/*
Author: Alex Glover
Date: 4-Nov-2012
*/
?>
<?php get_header(); ?>
<div id="content-area">
<?php
global $post;
$args = array( 'numberposts' => 5, 'category' => 47 );//CHANGE THESE VALUES AS NEEDED
$myposts = get_posts( $args );
foreach( $myposts as $post ) : setup_postdata($post); ?>
<h2><a href="<?php the_permalink(); ?>"><?php the_title(); ?></a></h2><br />
<?php if ( has_post_thumbnail()) : ?>
<a href="<?php the_permalink(); ?>" title="<?php the_title_attribute(); ?> " style="float:left; position: relative; margin: 0px 20px 0px 0px;">
<?php the_post_thumbnail('thumbnail'); ?>
</a><p style="width:450px; float:left; position: relative;">
<?php the_excerpt(); ?>
</p>
<br /><br /><br />
<?php endif; ?>
<?php endforeach; wp_reset_postdata(); ?>
<div></div>

</div> <!-- #content-area -->

<?php get_sidebar(); ?>
<?php get_footer(); ?>
```

<p>Here's a quick preview of what the 'page of posts' code looks like, but obviously aspects of this will be very different based on the theme that you are using.</p>
<p><a href="{{ "/assets/page_of_posts_screenshot.png" | absolute_url }}"><img class="aligncenter size-full wp-image-152" title="page_of_posts_screenshot" alt="" src="{{ site.baseurl }}/assets/page_of_posts_screenshot.png" width="595" height="477" /></a></p>
