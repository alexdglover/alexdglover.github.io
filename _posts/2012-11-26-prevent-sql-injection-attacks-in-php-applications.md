---

title: Prevent SQL Injection Attacks in PHP Applications
date: 2012-11-26 08:00:19.000000000 -06:00



categories:
- How-to Guides
- Utilities And Other Useful Things
tags:
- php
- sql injection
meta:
  _wpas_done_all: '1'
  _publicize_pending: '1'
  jabber_published: '1353940605'
  publicize_twitter_user: alexdglover
  email_notification: '1353940606'
  _wpas_done_1477652: '1'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:297624509;b:1;}}
  _wpas_done_1477650: '1'
  _wpas_skip_1477652: '1'
  _wpas_skip_1477650: '1'
  tagazine-media: a:7:{s:7:"primary";s:65:"http://alexdglover.files.wordpress.com/2012/11/rage-classic-l.png";s:6:"images";a:1:{s:65:"http://alexdglover.files.wordpress.com/2012/11/rage-classic-l.png";a:6:{s:8:"file_url";s:65:"http://alexdglover.files.wordpress.com/2012/11/rage-classic-l.png";s:5:"width";i:1500;s:6:"height";i:1224;s:4:"type";s:5:"image";s:4:"area";i:1836000;s:9:"file_path";b:0;}}s:6:"videos";a:0:{}s:11:"image_count";i:1;s:6:"author";s:8:"34836694";s:7:"blog_id";s:8:"34954019";s:9:"mod_stamp";s:19:"2012-11-26
    15:44:56";}
  reddit: a:2:{s:5:"count";i:0;s:4:"time";i:1354056474;}
  _edit_last: '1'
  _s2mail: 'yes'
---
**Be advised** This post is quite old (from 2012) and any code may be out of date. Proceed with caution.
{: .notice--warning}

<p>Ok, some quick background before we get started. Let's say you have a search bar in your PHP-based web site to help people find items, which ties back to your database. Behind your search bar is some code and a query, something like</p>

```php
$query = "SELECT * FROM ITEMS_TABLE WHERE ITEM_NAME LIKE" . $mySearchBarString . ";";
```
<p>So a user provides some string, like 'Xbox,' and it finds all items with 'Xbox' in the name. Lovely. But what if the user enters this:</p>

```sql
blah; SET @tables = NULL;
SELECT GROUP_CONCAT(table_schema, '.', table_name) INTO @tables FROM information_schema.tables;
SET @tables = CONCAT('DROP TABLE ', @tables);
PREPARE stmt1 FROM @tables;
EXECUTE stmt1;
DEALLOCATE PREPARE stmt1;
```

<p>For those who can't read SQL, here's the short version - an attacker just dropped ALL of your databases, in all schemas. Whoops. This is your face right now:<br />
<a href="http://alexdglover.files.wordpress.com/2012/11/rage-classic-l.png"><img class="aligncenter size-medium wp-image-241" title="rage-classic-l" alt="" src="{{ site.baseurl }}/assets/rage-classic-l.png?w=300" width="300" height="244" /></a></p>
<p>We're not going to let this happen to us, because losing and replacing that data seems like a lot of work, and we all have better things to do than restore backups and try to explain data loss to end users.</p>
<p>Let's review some options.</p>
<h3>Escape Special Characters</h3>
<p>Good news - this is a super easy method in PHP. Bad news - it doesn't protect against all SQL injection attacks. To implement, simply pass your users' input through the mysql_real_escape_string function and assign it to a new variable. Then use that variable in your query. For example, let's assume a user has posted some input from a form, which we'll grab with the $_POST[x] variable:</p>

```php
$userInput = mysql_real_escape_string($_POST[some_input]);
$query = "SELECT * FROM ITEMS_TABLE WHERE ITEM_NAME LIKE" . $userInput .";";
```

<p>Simple! However, mysql_real_escape_string was created primarily to sanitize inputs, not give 100% protection against SQL injection attacks. Clever attackers can input HTML into your database, setting you up for cross-site scripting attacks. mysql_real_escape_string also doesn't escape the percentage sign (%), the wildcard in SQL, leaving you vulnerable.</p>
<p>Just to be clear - don't use myql_escape_string, as it is also vulnerable to <a href="http://security.stackexchange.com/questions/9908/multibyte-character-exploits-php-mysql" target="_blank">multi-byte character attacks</a>. At the very least, use mysql_real_escape_string.</p>
<h3>MySQLi and Prepared Statements</h3>
<p>This post is already getting long-winded, so I'll be brief. Using prepared statements is a best practice for a boatload of reasons. The advantage is that you convert your users' string input into a parameter object. It is 100% immune to SQL injection, because the input isn't concatenated to the query, it's treated as an object. You can use either MySQLi or PDO prepared statements, both are acceptable. Here's a quick example of using MySQLi and prepared statements from my scratch-space website:</p>

```php
<?php
//set database connection
$mysqli = new mysqli("domain.com","username","password","database_name");

//open database connection; if connection fails, echo the mysql error
if ($mysqli->connect_errno) {
  echo "Failed to connect to MySQL: (" . $mysqli->connect_errno . ") " . $mysqli->connect_error;
}

//check if there is an HTTP POST variable; if one exists, then we need to do the insert; otherwise, don't insert anything
if (isset($_POST["orgName"])) {
  /* Prepared statement, stage 1: prepare */
  if (!($stmt = $mysqli->prepare("INSERT INTO appgen (orgName, motto, number, map, site, body) VALUES (?, ?, ?, ?, ?, ?)"))) {
    echo "Error while preparing statement";
  }
  /* Prepared statement, stage 2: bind and execute */
  if (!$stmt->bind_param("ssssss", $_POST[orgName], $_POST[motto], $_POST[number], $_POST[map], $_POST[site], $_POST[body])) {
    echo "Binding parameters failed: (" . $stmt->errno . ") " . $stmt->error;
  }

  if (!$stmt->execute()) {
    echo "Execute failed: (" . $stmt->errno . ") " . $stmt->error;
  }
  else{
    echo "<script>alert('Record added!');</script>";
  }
}
?>
```

<p>I think most of the code is pretty straightforward, but one thing that tripped me up is the "bind_param" function. The first argument in quotes is actually the data types of all of the parameters you want to bind. Each letter, in sequential order, represents the datatype  of the corresponding parameter. There are only four supported datatypes, i for integer, d for double, s for string, and b for <a href="http://en.wikipedia.org/wiki/Binary_large_object" target="_blank">BLOB</a>. So, for example, if I wanted to prepare an insert statement to insert an integer primary key, a string name, a decimal price, and an image BLOB, my code would look something like this:</p>

```php
<?php
...
if (!($stmt = $mysqli->prepare("INSERT INTO table (id, name, price, image) VALUES (?, ?, ?, ?)"))) {
  echo "Error while preparing statement";
}
/* Prepared statement, stage 2: bind and execute */
if (!$stmt->bind_param("isdb", 1,"Xbox", 299.99, image_binary)) {
  echo "Binding parameters failed: (" . $stmt->errno . ") " . $stmt->error;
}
...
?>
```

<p>Also notice that the number of question marks should be the same as the number of parameters you are binding. The <a href="http://php.net/manual/en/mysqli.quickstart.prepared-statements.php" target="_blank">documentation at php.net</a> is pretty good, but not great. If you have any questions or issues with this, please feel free to leave a comment.</p>
<p>As always, hope this was helpful!</p>
