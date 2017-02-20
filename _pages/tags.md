---
layout: archive
permalink: /tags/
title: "Posts by Tag"
author_profile: false
---

<script>
window.onload = function(){
  getTagFromUrl();
};

function getPostsByTag(val) {

  console.log("hiding all elements");
  $( ".postGroup" ).hide();

  // Downcase query val for case-insensitive search
  val = val.toLowerCase();

  console.log("display div with matching ID");
  $( "div[id=" + val + "]" ).show()

} // End of getSearchResults

function getTagFromUrl(){
  var fullUrl = window.location.href;
  var tag = fullUrl.split('#')[1];
  getPostsByTag(tag);
}

</script>

{% include group-by-array collection=site.posts field="tags" %}

{% for tag in group_names %}
  {% assign posts = group_items[forloop.index0] %}
  <div id="{{ tag | slugify | downcase }}" class="postGroup" style="display: none;">
    <h2 class="archive__subtitle">Tag: {{ tag | downcase }}</h2>
    {% for post in posts %}
      {% include archive-single.html %}
    {% endfor %}
  </div>
{% endfor %}
