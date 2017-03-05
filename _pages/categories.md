---
layout: archive
permalink: /categories/
title: "Posts by Category"
author_profile: false
---

<script>
window.onload = function(){
  getCategoryFromUrl();
};

function getPostsByCategory(val) {

  console.log("hiding all elements");
  $( ".postGroup" ).hide();

  // Downcase query val for case-insensitive search
  val = val.toLowerCase();

  console.log("display div with matching ID");
  $( "div[id*='" + val + "']" ).show()

} // End of getSearchResults

function getCategoryFromUrl(){
  var fullUrl = window.location.href;
  var category = fullUrl.split('#')[1];
  getPostsByCategory(category);
}

</script>

{% include group-by-array collection=site.posts field="categories" %}

{% for category in group_names %}
  {% assign posts = group_items[forloop.index0] %}
  <div id="{{ category | slugify | downcase }}" class="postGroup" style="display: none;">
    <h2 class="archive__subtitle">Category: {{ category | downcase }}</h2>
    {% for post in posts %}
      {% include archive-single-experimental.html %}
    {% endfor %}
  </div>
{% endfor %}
