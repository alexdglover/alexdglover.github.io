---
title: 'To Do List App'
date: 2013-04-03 17:01:55.000000000 -05:00
categories:
- Utilities And Other Useful Things
tags:
- asynchronous
- jQuery
- mysql
- mysqli
- php
- to do app
- todomvc
---
**Be advised** This post is quite old ({{ page.date | date_to_string }}) and any code may be out of date. Proceed with caution.
{: .notice--warning}

**Update Feb 2017** I've happily used this TODO app for the past ~4 years, but I'm taking it down with the rest of my old PHP hosting. In conjunction, I've removed the demo-related content from this post. For anyone interested, I'm now using [Wunderlist](http://www.wunderlist.com/)
{: .notice--info}

<p>Short post, as this isn't really a tutorial. I was recently checking out <a href="http://todomvc.com/" target="_blank">ToDoMVC</a> to look at some JavaScript frameworks, and I really liked the To Do list app...</p>
<p><img class="aligncenter size-full wp-image-544" alt="todosmvc" src="{{ site.baseurl }}/assets/todos.png" width="682" height="534" /></p>
<p>I've been looking for something similar for awhile. Looked at Evernote and other apps, but nothing was as simple and lightweight as this app. Only problem was that the items stored in the app didn't persist, as there was not database/storage behind the app. So I went ahead and wrote my own.</p>

[GitHub Repo](https://github.com/alexdglover/vigilant-winner){: .btn .btn--large .btn--info}
{: .text-center}

<p>In my haste to modify the to do list app to save values, I skipped the part where I should have learned one of the new JavaScript frameworks highlighted on <a href="http://todomvc.com/" target="_blank">ToDoMVC</a>. I actually used the template provided in the ToDoMVC package and used the asynchronous database call setup I outlined in a <a title="Asynchronous Database Operations with PHP and jQuery" href="http://alexdglover.com/asynchronous-database-operations-with-php-and-jquery/" target="_blank">related post</a>.</p>
<p>The bad news - this isn't a secure application, SQL injection attacks will succeed here. Also, some of the icons don't seem to work outside of Chrome.</p>
<p>The good news - this application supports multiple clients via the use of a "poor man's pull." Basically, if User A and User B are writing to dos in the app on two separate computers, they will see each other's notes in near real time. This is possible by using JavaScript to call the DB query every X number of milliseconds. Here's a code snippet if you're having a hard time understanding:</p>
```javascript
<script>
setInterval( function(){ queryDB() }, 5000);   //Call the queryDB() function every 5 seconds
</script>

<script type="text/javascript">        
function queryDB()
{
  $.ajax({
    type: "POST",
    url: "dbEngine.php",
    data: "keyword=read",
    success: function(server_response)
    {
      $('#todo-list').html(server_response).show();
    }
  })
}
</script>
```
<p>The app also works on mobile phones, which is great.</p>
<p>If you want to set up your own instance of this app, you can <a href="https://github.com/alexdglover/vigilant-winner" target="_blank">download the code from GitHub</a>. Obviously you'll need your own hosting and a MySQL database, but the rest should be pretty easy to figure out.</p>
<p>This isn't a fully finished product by any means, but I wanted to put it out there in case there's someone like me who needs a simple to-do list app.</p>
