---
title: Search
permalink: /search
---

<script>

function getSearchResults(val) {
  console.log("hiding all elements");
  $( ".result" ).hide();
  $( ".postContent" ).hide();
  $( ".searchResultSnippet " ).hide();

  // Don't bother doing anything until at least 3 characters
  // have been entered
  if(val.length > 2){
    // Downcase query val for case-insensitive search
    val = val.toLowerCase();

    // Show any results based on post title matching query
    $( "div[id*='" + val + "' i]" ).filter( ".result").show();

    // Show any results where query matches part of the post content
    $( "div:contains('" + val + "')" ).filter( ".result").show();

    // For each result based on post content, extract the surrounding text
    // and display it in the search results, bolding the search term
    $( "div:contains('" + val + "')" ).filter( ".result").each( function( index ) {
      var postText = $( this ).find( ".postContent" ).text();
      var resultPosition = postText.indexOf(val);
      if(resultPosition > -1){
        var snippet = "..." + postText.substring( resultPosition-30, resultPosition+30 ) + "...";
        snippet = snippet.replace(val, "<strong>" + val + "</strong>");
        $( this ).find( ".searchResultSnippet" ).show().html(snippet);
      }
    });
  } // End of input length check
  
} // End of getSearchResults
</script>

<input type="text" onkeyup="getSearchResults(this.value)">

<div id="results">
  {% for post in site.posts %}
  <div id="{{ post.title }}" class="result" style="display: none;"> 
    <h3><a href="{{ post.url }}">{{ post.title }}</a></h3>
    {% if post.header.teaser %}
    <a href="{{ post.url }}"><img src="{{ post.header.teaser }}" style="width: 150px;" class="align-left" /></a>
    {% endif %}
    <p><small><strong>{{ post.date | date: "%B %e, %Y" }}</strong> - <strong>Categories</strong> {{ post.categories | join:',' }} </small></p>
    <div class="postContent" style="display:none;">Categories: {{ post.categories | join:',' | downcase}} , {{ post.content | strip_html | downcase }}</div>
    <div class="searchResultSnippet" style="display:none;"></div>
  </div>
  {% endfor %}
</div>
