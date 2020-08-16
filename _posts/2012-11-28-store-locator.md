---
title: Easily Create a Store Locator
date: 2012-11-28 08:15:41.000000000 -06:00
categories:
- Utilities And Other Useful Things
tags:
- store locator
- web development
excerpt_separator: <!--more-->
---
<p>I had initially set out to build my own custom store locator (just for the learning experience) and post a how-to article for it. I quickly found that this has been done many times before, and Google has a <a href="https://developers.google.com/maps/articles/phpsqlsearch_v3" target="_blank">very good tutorial</a> if you want to build your own.</p>
<p>But what if you don't have a database behind your website? What if you don't have any IT staff to support a custom store locator? What if you need an <em>easy </em>solution?</p>
<p>Check out <a href="http://batchgeo.com/" target="_blank">BatchGeo</a>, a free (with ads) hosted store locator service. All you need is a spreadsheet of your locations that you want to map and whatever secondary information you want to display. BatchGeo takes care of mapping the addresses, so you don't have to determine the latitude and longitude values for each address (like in the Google tutorial). You can also group your locations arbitrarily, such as by location type by simply adding another column of data.</p>
<!--more-->
<p style="text-align: center;"><a href="{{ site.baseurl }}/assets/alexdglover_batchgeo_map_demo.png"><img class="aligncenter size-medium wp-image-280" title="alexdglover_batchgeo_map_demo" alt="alexdglover_batchgeo_map_demo" src="{{ site.baseurl }}/assets/alexdglover_batchgeo_map_demo.png?w=297" height="300" width="297" /></a></p>
<p>Once you supply your information and click "Map Now," the map will be generated for you to preview. If all is well, just click Save & Continue. You'll be asked to provide an email address and some basic information about the map you generated, including whether you want it to be public or unlisted. Then just click Save Map.</p>
<p>Finally, BatchGeo will email you the new map URL, as well as some basic iframe code to embed your map on your website. The map has great functionality, allowing you to search geographically (by city, zip code, whatever) or by secondary content (store name, group name, even phone number). Content display is great. Markers are lettered to correspond to the location listing below (as we've come to expect from Google maps). Markers are also color coded if you used grouping. Info windows cleanly display formatted secondary information such as web site and phone number.</p>
<p>If you want to get rid of the embedded advertisements, unfortunately it's going to cost you $99.00 per month. If you can bear the ads, it's an awesome free service.</p>
