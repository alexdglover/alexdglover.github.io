---
title: Asynchronous Database Operations with PHP and jQuery
date: 2013-03-26 08:00:57.000000000 -05:00
categories:
- Utilities And Other Useful Things
tags:
- asynchronous
- database
- javascript
- jQuery
- php

---
**Be advised** This post is quite old ({{ page.date | date_to_string }}) and any code may be out of date. Proceed with caution.
{: .notice--warning}

<p>In a recent work project, we had to do some simple database calls to update records. I was going to do a simple form with a submit button, but decided checked in with one of my coworkers (who's a far better developer than I) to see what he thought. Since we were developing a 'flashier' application, he pushed me to do it all asynchronously with jQuery. I had a hard time finding some good examples at the time, so I decided to create some demos and do a write up.</p>
<p>Before we start looking at the code, I need to mention two things. For this walk-through, we'll start with a simple PHP form and add in jQuery components. If this doesn't interest you, feel free to skip down to the final section. Second, if you want to follow my example to the letter, you'll need to setup a database (I'm using MySQL) and add a table 'customer' with the following structure:</p>
<p><a href="http://alexdglover.com/wp-content/uploads/2013/03/alexdglover_asynchDatabaseDemo_tableStructure.png"><img class="aligncenter size-full wp-image-525" alt="alexdglover_asynchDatabaseDemo_tableStructure" src="{{ site.baseurl }}/assets/alexdglover_asynchDatabaseDemo_tableStructure.png" width="564" height="229" /></a></p>
<p>Here's a quick script to create the table if you're feeling lazy:</p>

```sql
CREATE TABLE `customer` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `Last_Name` varchar(50) COLLATE latin1_general_ci NOT NULL,
  `First_Name` varchar(50) COLLATE latin1_general_ci NOT NULL,
  `Phone_Number` varchar(10) COLLATE latin1_general_ci NOT NULL,
  `Street_Address` varchar(50) COLLATE latin1_general_ci NOT NULL,
  `City` varchar(50) COLLATE latin1_general_ci NOT NULL,
  `State` varchar(25) COLLATE latin1_general_ci NOT NULL,
  `ZIP` varchar(5) COLLATE latin1_general_ci NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=MyISAM  DEFAULT CHARSET=latin1 COLLATE=latin1_general_ci AUTO_INCREMENT=19 ;
```

<p>Of course you can use a different database, different table name, columns etc., just keep that in mind as you write your code.</p>
<h2>Part 1 - Simple PHP Form and Submit Button</h2>
<p>Before we add anything new, let's just get a basic page working. This page will have a form with a text field input for each field in the database (except the ID, since it auto-increments), and a submit button. The page will also have a simple table to show the records currently in the database. If you're totally new to PHP forms and MySQL, check out the W3Schools PHP tutorials on <a href="http://www.w3schools.com/php/php_forms.asp" target="_blank">forms</a>, <a href="http://www.w3schools.com/php/php_get.asp" target="_blank">$_GET</a>/<a href="http://www.w3schools.com/php/php_post.asp" target="_blank">$_POST</a>, <a href="http://www.w3schools.com/php/php_mysql_insert.asp" target="_blank">INSERT</a> and <a href="http://www.w3schools.com/php/php_mysql_select.asp" target="_blank">SELECT</a>.</p>

```php
<head>

<?php
$con=mysqli_connect("<hostname>","<username>","<password>","<database_name>");
// Check connection
if (mysqli_connect_errno())
  {
  echo "Failed to connect to MySQL: " . mysqli_connect_error();
  }

if (isset($_POST["lastName"])) {
  $sql="INSERT INTO customer (Last_Name, First_Name, Phone_Number, Street_Address, City, State, Zip) VALUES ('$_POST[lastName]', '$_POST[firstName]', '$_POST[phoneNumber]', '$_POST[address]', '$_POST[city]', '$_POST[state]', '$_POST[zip]')";
  if (!mysqli_query($con,$sql))
  {
    die('Error: ' . mysqli_error());
  }
  echo "1 record added";
}
?>

<title>Alex Glover's Portfolio Site</title>
</head>
<body>
<h1>Asynchronous Database Calls Demo</h1>

<form action="asynchDatabaseDemo.php" method="post">
Last Name: <input type="text" name="lastName"><br />
First Name: <input type="text" name="firstName"><br />
Phone: <input type="text" name="phoneNumber"><br />
Address: <input type="text" name="address"><br />
City: <input type="text" name="city"><br />
State: <input type="text" name="state"><br />
ZIP: <input type="text" name="zip"><br />
<button type="submit">Submit</button>
</form>

<br />
<br />
<br />

<table>
<tr><th>Last Name</th><th>First Name</th><th>Phone Number</th><th>Address</th><th>City</th><th>State</th><th>Zip</th></tr>

<?php
/* Get the entire appgen table to fill out the table on the page */
$query = "SELECT * FROM customer order by Last_Name desc;";
if($result = $con->query($query)) {
  while ($row = $result->fetch_row()){
    echo "<tr>";
    echo "<td>" . $row[1] . "</td>";
    echo "<td>" . $row[2] . "</td>";
    echo "<td>" . $row[3] . "</td>";
    echo "<td>" . $row[4] . "</td>";
    echo "<td>" . $row[5] . "</td>";
    echo "<td>" . $row[6] . "</td>";
    echo "<td>" . $row[7] . "</td>";
    echo "</tr>";
  }
  $result->close();
}
?>
</table>
</body>
</html>
```

<p>So now we can insert new records, and see all the records in the table. This is all Web Development 101, but it's important that we've built a working foundation. This way if we have issues later we can narrow it down to JavaScript or database issues.</p>
<h2>Part 2 - Introducing jQuery and Serialize</h2>
<p>OK, we're going to take a small step. Instead of using a button within a form to submit the page and all of the form values, we'll use JavaScript. Conveniently, jQuery has a <a href="http://api.jquery.com/serialize/" target="_blank">serialize</a> function we can use to grab all of the input values at once and format them in a convenient string. Then we'll use an ajax call to submit that pre-formatted string to the server.</p>
<p>I haven't removed any of the original code, simply added one JavaScript function insert() and one anchor tag that will call the insert function. The insert() function will actually POST the values back to the server, back to the very same page (see the url: "asynchDatabaseDemo2.php" line). Since our PHP is looking for POST values, it will 'catch' them and insert them into the database. Let's review the code:</p>

```php
<head>

<?php
$con=mysqli_connect("<hostname>","<username>","<password>","<database_name>");
// Check connection
if (mysqli_connect_errno())
  {
  echo "Failed to connect to MySQL: " . mysqli_connect_error();
  }

if (isset($_POST["lastName"])) {
  $sql="INSERT INTO customer (Last_Name, First_Name, Phone_Number, Street_Address, City, State, Zip) VALUES ('$_POST[lastName]', '$_POST[firstName]', '$_POST[phoneNumber]', '$_POST[address]', '$_POST[city]', '$_POST[state]', '$_POST[zip]')";
  if (!mysqli_query($con,$sql))
  {
    die('Error: ' . mysqli_error());
  }
  echo "1 record added";
  //mysqli_close($con);
}
?>

<title>Alex Glover's Portfolio Site</title>
<script src="http://code.jquery.com/jquery-1.9.1.js"></script>
<script type="text/javascript">    
function insert()
{
  var formValues = $('form').serialize();

  alert(formValues);
  $.ajax({
    type: "POST",
    url: "asynchDatabaseDemo2.php",
    data: formValues
  })
}
</script>

</head>
<body>
<h1>Asynchronous Database Calls Demo</h1>

Search:<input type="text" onkeydown="search()"><button type="submit"></button>

<br />
<br />
<br />

<form action="asynchDatabaseDemo.php" method="post">
Last Name: <input type="text" name="lastName"><br />
First Name: <input type="text" name="firstName"><br />
Phone: <input type="text" name="phoneNumber"><br />
Address: <input type="text" name="address"><br />
City: <input type="text" name="city"><br />
State: <input type="text" name="state"><br />
ZIP: <input type="text" name="zip"><br />
<button type="submit">Submit</button>
<a href="#" onclick="insert()">jQuery Insert</button>
</form>

<br />
<br />
<br />

<table>
<tr><th>Last Name</th><th>First Name</th><th>Phone Number</th><th>Address</th><th>City</th><th>State</th><th>Zip</th></tr>

<?php
/* Get the entire appgen table to fill out the table on the page */
$query = "SELECT * FROM customer order by Last_Name desc;";
if($result = $con->query($query)) {
  while ($row = $result->fetch_row()){
    echo "<tr>";
    echo "<td>" . $row[1] . "</td>";
    echo "<td>" . $row[2] . "</td>";
    echo "<td>" . $row[3] . "</td>";
    echo "<td>" . $row[4] . "</td>";
    echo "<td>" . $row[5] . "</td>";
    echo "<td>" . $row[6] . "</td>";
    echo "<td>" . $row[7] . "</td>";
    echo "</tr>";
  }
  $result->close();
}
?>
</table>
</body>
</html>
```

<p>Simple right? The data is inserted, we see the alert that shows the serialized string... but the table doesn't show the new values. No worries, we just need to make the table operate asynchronously as well.</p>
<h2>Part 3 - Asynchronous Search Results</h2>
<p>This is a slightly more complicated step, so a couple quick notes before we get started:</p>
<ul>
<li><span style="line-height: 13px;">JavaScript can't call the PHP directly to re-query the database; JavaScript is a client-side language, PHP is a server-side language. However, we can 'cheat' by using HTTP POST or GET requests to act as a bridge between the two. To do this we need to create a separate PHP page to handle the searching, called "asynchDatabaseDemo_DBEngine.php". This page will echo table tags identical to our original table structure</span></li>
<li>We'll add a new JavaScript function, searchDB(), that will POST to the PHP page dedicated to querying the database. Then we'll add a search field that calls the searchDB() function. Finally, we'll modify the insert() function to call the searchDB() function - that way the table will be refreshed as soon as the insert completes</li>
<li>The ajax call within the searchDB() function has an additional attribute, 'success' that will handle the server response by placing the HTML within the table tags (by referencing the ID '#searchResults')</li>
</ul>
<p>Alright, hopefully that will make the code make a little more sense. Let's look at the asynchDatabaseDemo_DBEngine.php file to start:</p>

```php
<?php
$con=mysqli_connect("<hostname>","<username>","<password>","<database_name>");
// Check connection
if (mysqli_connect_errno())
{
  echo "Failed to connect to MySQL: " . mysqli_connect_error();
}

if (isset($_POST["keyword"]))
{
  $keyword = $_POST["keyword"];
  $query = "SELECT * FROM customer where Last_Name like '%$keyword%' order by Last_Name desc;";
  if($result = $con->query($query))
  {
    echo "<tr><th>Last Name</th><th>First Name</th><th>Phone Number</th><th>Address</th><th>City</th><th>State</th><th>Zip</th></tr>";

    while ($row = $result->fetch_row()){
      echo "<tr>";
      echo "<td>" . $row[1] . "</td>";
      echo "<td>" . $row[2] . "</td>";
      echo "<td>" . $row[3] . "</td>";
      echo "<td>" . $row[4] . "</td>";
      echo "<td>" . $row[5] . "</td>";
      echo "<td>" . $row[6] . "</td>";
      echo "<td>" . $row[7] . "</td>";
      echo "</tr>";
    }
    $result->close();
  }
  else
  {
    echo 'No Results for :"'.$_POST['keyword'].'"';
  }

}
?>
```

<p>And finally the aynchDatabaseDemo_frontEnd.php code:</p>

```php
<head>

<?php
$con=mysqli_connect("<hostname>","<username>","<password>","<database_name>");
// Check connection
if (mysqli_connect_errno())
  {
  echo "Failed to connect to MySQL: " . mysqli_connect_error();
  }

if (isset($_POST["lastName"])) {
  $sql="INSERT INTO customer (Last_Name, First_Name, Phone_Number, Street_Address, City, State, Zip) VALUES ('$_POST[lastName]', '$_POST[firstName]', '$_POST[phoneNumber]', '$_POST[address]', '$_POST[city]', '$_POST[state]', '$_POST[zip]')";
  if (!mysqli_query($con,$sql))
  {
    die('Error: ' . mysqli_error());
  }
  echo "1 record added";
  //mysqli_close($con);
}
?>

<title>Alex Glover's Portfolio Site</title>
<script src="http://code.jquery.com/jquery-1.9.1.js"></script>
<script type="text/javascript">    
function insert()
{
  var formValues = $('form').serialize();

  alert(formValues);

  $.ajax({
    type: "POST",
    url: "asynchDatabaseDemo_frontEnd.php",
    data: formValues
  })

  queryDB();

  $(this).closest('form').find("input[type=text], textarea").val("");
}
</script>
<script type="text/javascript">    
function queryDB()
{
  var keyword = $("#searchField").val();

  $.ajax({
    type: "POST",
    url: "asynchDatabaseDemo_DBEngine.php",
    data: "keyword=" + keyword,
    success: function(server_response)
    {
      $('#searchResults').html(server_response).show();
    }
  })
}
</script>
</head>
<body>
<h1>Asynchronous Database Calls Demo</h1>

Search:<input type="text" onkeydown="queryDB()" id="searchField"><a href="#" onclick="queryDB()">Search</a>

<br />
<br />
<br />

<form action="asynchDatabaseDemo.php" method="post">
Last Name: <input type="text" name="lastName"><br />
First Name: <input type="text" name="firstName"><br />
Phone: <input type="text" name="phoneNumber"><br />
Address: <input type="text" name="address"><br />
City: <input type="text" name="city"><br />
State: <input type="text" name="state"><br />
ZIP: <input type="text" name="zip"><br />
<button type="submit">Submit</button>
<a href="#" onclick="insert()">jQuery Insert</button>
</form>

<br />
<br />
<br />

<table id="searchResults">
<tr><th>Last Name</th><th>First Name</th><th>Phone Number</th><th>Address</th><th>City</th><th>State</th><th>Zip</th></tr>

<?php
/* Get the entire appgen table to fill out the table on the page */
$query = "SELECT * FROM customer order by Last_Name desc;";
if($result = $con->query($query)) {
  while ($row = $result->fetch_row()){
    echo "<tr>";
    echo "<td>" . $row[1] . "</td>";
    echo "<td>" . $row[2] . "</td>";
    echo "<td>" . $row[3] . "</td>";
    echo "<td>" . $row[4] . "</td>";
    echo "<td>" . $row[5] . "</td>";
    echo "<td>" . $row[6] . "</td>";
    echo "<td>" . $row[7] . "</td>";
    echo "</tr>";
  }
  $result->close();
}
?>
</table>
</body>
</html>
```
<p>Done! This tutorial came out a little more scatter-brained than I had hoped, so feel free to comment/contact me if you have any questions. As always, I hope this helps.</p>
