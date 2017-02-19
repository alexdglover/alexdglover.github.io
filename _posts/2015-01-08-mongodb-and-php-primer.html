---

title: MongoDB and PHP Primer
date: 2015-01-08 09:30:24.000000000 -06:00



categories:
- Fun IT Projects
- How-to Guides
tags:
- mongodb
- nosql
- php
meta:
  _edit_last: '1'
  _s2mail: 'yes'
  _publicize_twitter_user: "@alexdglover"
  _wpas_done_all: '1'
  _wpas_skip_1477652: '1'
  _wpas_skip_1477650: '1'
---
<p>I recently used MongoDB in the Share Your Salary app and had a few hiccups as I tried to blunder my way through learning how to use it. MongoDB, like many NoSQL document databases, uses JavaScript Object Notation or JSON to represent the objects/documents. JSON is great, especially when you're using JavaScript in conjunction. PHP, however, doesn't 'speak' JSON natively; it uses arrays instead, and this makes for some less than intuitive interactions. This post will go over some of the basic CRUD operations using PHP against a MongoDB.</p>
<h3>Connecting</h3>
<p>Connecting is pretty straight-forward:</p>
<pre class="lang:php decode:true">$host = your.mongodb.host.com;
$user = username;
$passwd = Sup3rS3cr3tPassword;
$port = 27017; // 27017 is the default port
$uri = "mongodb://" . $user . ":" . $passwd . "@" . $host . ":" . $port;
$mongo = new Mongo($uri);
</pre>
<p>If you're using <a title="Introduction to OpenShift" href="http://alexdglover.com/introduction-to-openshift/" target="_blank">OpenShift</a>, you can simply use the relevant environment variables:</p>
<pre class="lang:php decode:true ">$host = $_ENV["OPENSHIFT_MONGODB_DB_HOST"];
$user = $_ENV["OPENSHIFT_MONGODB_DB_USERNAME"];
$passwd = $_ENV["OPENSHIFT_MONGODB_DB_PASSWORD"];
$port = $_ENV["OPENSHIFT_MONGODB_DB_PORT"];
$uri = "mongodb://" . $user . ":" . $passwd . "@" . $host . ":" . $port;
$mongo = new MongoClient($uri);</pre>
<p><!--more--></p>
<h3>Reading a 'table'</h3>
<p>In NoSQL document databases, an individual document is analogous to a record in a traditional relational SQL database. Similarly, a collection is analogous to a table.</p>
<p>Before we can fetch a collection, we have to specify which database we want to work with. After all, one MongoDB instance can host multiple databases.</p>
<pre class="lang:php decode:true">$db = $mongo-&gt;database_name;
$collection = $db-&gt;selectCollection("collection_name");
</pre>
<p>Or if you want to save some typing:</p>
<pre class="lang:php decode:true">$collection = $mongo-&gt;selectCollection("database_name", "collection_name");
</pre>
<p>Now that you have a collection object, you can query it, iterate through it, etc.</p>
<h3>Querying documents</h3>
<p>When querying MongoDB, a cursor object is returned. This cursor must be iterated to get to the actual data in the records returned.</p>
<p>To get all documents in a collection, just call the find() method:</p>
<pre class="lang:php decode:true">$cursor = $mongo-&gt;database_name-&gt;collection_name-&gt;find();</pre>
<p>This is similar to a "SELECT * FROM table_name" query in a relational SQL database.</p>
<p>To query with specific criteria, you'll need to construct an array of the key-value pair you want to query on. Let's assume we had a few JSON documents with the following structure:</p>
<pre class="lang:js decode:true">{ "name": "alex", "age": 28, "computers": ["laptop","desktop","vm"] },
{ "name": "whoever", "age": 35, "computers": ["laptop","desktop"] }</pre>
<p>To search the "name" keys for the value "alex" you'll need to create an array with that key-value pair, then execute the find method with that query.</p>
<pre class="lang:php decode:true">$query = array('name' =&gt; 'alex');
$cursor = $collection-&gt;find($query);
</pre>
<p>Or, more succinctly:</p>
<pre class="lang:php decode:true">$cursor = $collection-&gt;find(array('name' =&gt; 'alex'));
</pre>
<p>This is similar to a "SELECT * FROM table_name where name='alex' " query in a relational SQL database.</p>
<p>But what if you wanted something less specific, like all documents where the 'name' value started with 'a' ? In a relational SQL query, you would used the LIKE operator and wildcards (%). With MongoDB, you just use regular expressions in your query. So, to get all documents where the 'name' value started with 'a' you would execute:</p>
<pre class="lang:php decode:true">$query = array('name' =&gt; array('$regex' =&gt; new MongoRegex("/^a.*/")));
$cursor = $collection-&gt;find($query);</pre>
<p>If you're not familiar with regular expressions, spend some time at <a href="https://regex101.com/">Regex101</a>. Super useful, pretty easy to learn.</p>
<p>If you want to return a single result array instead of a cursor, use the <a href="http://php.net/manual/en/mongocollection.findone.php">findOne()</a> method.</p>
<h3>Inserting a new document</h3>
<p>Inserting a new row/document is really easy. Since the collection (and all of MongoDB for that matter) doesn't have any schema or structure to enforce, you can insert any array into any collection, so long as it's a valid array.</p>
<pre class="lang:php decode:true">$collection-&gt;insert(array('name' =&gt; 'bob','age' =&gt; 40 ));
$collection-&gt;insert(array('someUnrelatedKey' =&gt; 'someUnrelatedValue'));
</pre>
<p>You can also inserted nested arrays (which will become nested objects in JSON):</p>
<pre class="lang:php decode:true">$collection-&gt;insert(array('name' =&gt; 'bob','age' =&gt; 40, 'computers' =&gt;(array('laptop','desktop')) ));</pre>
<h3>Updating an existing document</h3>
<p>Updating a document is where MongoDB shows it's flexibility as a NoSQL database. It's also the trickiest of the CRUD operations. The basic structure with psuedo-code is as follows:</p>
<pre class="lang:php decode:true">$collection-&gt;update(search_criteria, array('$set' =&gt; array("key =&gt; "value") ));</pre>
<p>Here's an example with legitimate values:</p>
<pre class="lang:php decode:true">$collection-&gt;update(array("name" =&gt; "alex"), array('$set' =&gt; array("name" =&gt; "bob")));</pre>
<p>This query is equivalent to "UPDATE collection SET name='bob' where name='alex';" in traditional SQL.</p>
<p>Notice the $set argument - without this, MongoDB will REPLACE the entire document with the array you passed instead of trying to update a particular element. There are several <a href="http://docs.mongodb.org/manual/reference/operator/update/">update operators for MongoDB</a> that can be very helpful. For example, let's assume we had the following document object:</p>
<pre class="lang:js decode:true">{ "name": "bob", "age": 35, "computers": ["laptop","desktop"] }</pre>
<p>Notice that we have a nested array of "computers" with two values. If we wanted to add another element to the "computers" array, we'd have to fetch the entire document, strip out the nested array object, add our new element, then use the $set operator to update the array in the document.</p>
<p>Fortunately, MongoDB provides a special update operator for this use case, $push. Let's pretend we want to update bob's record in the database and give him a VM.</p>
<pre class="lang:php decode:true">$collection-&gt;update(array('name' =&gt; 'bob'),array('$push' =&gt; array('computers' =&gt; 'vm')));
</pre>
<p>Awesome.</p>
<p>This was a pretty elementary tutorial on MongoDB, but I'm hoping it either helped one of you get started or inspired someone to use MongoDB in their project. Everything I covered in this post and much more can be found by perusing the <a href="http://php.net/manual/en/book.mongo.php" target="_blank">Mongo Book at php.net</a> and <a href="http://docs.mongodb.org/manual/reference/" target="_blank">MongoDB's Reference</a>.</p>
