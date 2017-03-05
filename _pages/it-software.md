---
layout: archive
permalink: /it-software-projects/
title: "IT/Software Projects"
---
<div class="postGroup"> 
  <h2 class="archive__subtitle">Recent posts</h2>
  {% for post in site.categories['IT/Software Projects'] %}
    {% include archive-single-experimental.html %}
  {% endfor %}
</div>
