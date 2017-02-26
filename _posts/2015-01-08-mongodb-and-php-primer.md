---
title: MongoDB and PHP Primer
date: 2015-01-08 09:30:24.000000000 -06:00
categories:
- IT/Software Projects
- How-to Guides
tags:
- mongodb
- nosql
- php
---
<p>I recently used MongoDB in the Share Your Salary app and had a few hiccups as I tried to blunder my way through learning how to use it. MongoDB, like many NoSQL document databases, uses JavaScript Object Notation or JSON to represent the objects/documents. JSON is great, especially when you're using JavaScript in conjunction. PHP, however, doesn't 'speak' JSON natively; it uses arrays instead, and this makes for some less than intuitive interactions. This post will go over some of the basic CRUD operations using PHP against a MongoDB.</p>
<h3>Connecting</h3>
<p>Connecting is pretty straight-forward:</p>
```php
$host = your.mongodb.host.com;
$user = username;
$passwd = Sup3rS3cr3tPassword;
$port = 27017; // 27017 is the default port
$uri = "mongodb://" . $user . ":" . $passwd . "@" . $host . ":" . $port;
$mongo = new Mongo($uri);
```
<p>If you're using <a title="Introduction to OpenShift" href="http://alexdglover.com/introduction-to-openshift/" target="_blank">OpenShift</a>, you can simply use the relevant environment variables:</p>
```php
$host = $_ENV["OPENSHIFT_MONGODB_DB_HOST"];
$user = $_ENV["OPENSHIFT_MONGODB_DB_USERNAME"];
$passwd = $_ENV["OPENSHIFT_MONGODB_DB_PASSWORD"];
$port = $_ENV["OPENSHIFT_MONGODB_DB_PORT"];
$uri = "mongodb://" . $user . ":" . $passwd . "@" . $host . ":" . $port;
$mongo = new MongoClient($uri);
```

<h3>Reading a 'table'</h3>
<p>In NoSQL document databases, an individual document is analogous to a record in a traditional relational SQL database. Similarly, a collection is analogous to a table.</p>
<p>Before we can fetch a collection, we have to specify which database we want to work with. After all, one MongoDB instance can host multiple databases.</p>
```php
database_name;
$collection = $db->selectCollection("collection_name");
```
<p>Or if you want to save some typing:</p>
```
selectCollection("database_name", "collection_name");
```
<p>Now that you have a collection object, you can query it, iterate through it, etc.</p>
<h3>Querying documents</h3>
<p>When querying MongoDB, a cursor object is returned. This cursor must be iterated to get to the actual data in the records returned.</p>
<p>To get all documents in a collection, just call the find() method:</p>
```
$cursor = $mongo->database_name->collection_name->find();
```
<p>This is similar to a "SELECT * FROM table_name" query in a relational SQL database.</p>
<p>To query with specific criteria, you'll need to construct an array of the key-value pair you want to query on. Let's assume we had a few JSON documents with the following structure:</p>
```javascript
{ "name": "alex", "age": 28, "computers": ["laptop","desktop","vm"] },
{ "name": "whoever", "age": 35, "computers": ["laptop","desktop"] }
```
<p>To search the "name" keys for the value "alex" you'll need to create an array with that key-value pair, then execute the find method with that query.</p>
```php
$query = array('name' => 'alex');
$cursor = $collection->find($query);
```
<p>Or, more succinctly:</p>
```php
cursor = $collection->find(array('name' => 'alex'));
```
<p>This is similar to a "SELECT * FROM table_name where name='alex' " query in a relational SQL database.</p>
<p>But what if you wanted something less specific, like all documents where the 'name' value started with 'a' ? In a relational SQL query, you would used the LIKE operator and wildcards (%). With MongoDB, you just use regular expressions in your query. So, to get all documents where the 'name' value started with 'a' you would execute:</p>
```php
$query = array('name' => array('$regex' => new MongoRegex("/^a.*/")));
$cursor = $collection->find($query);
```
<p>If you're not familiar with regular expressions, spend some time at <a href="https://regex101.com/">Regex101</a>. Super useful, pretty easy to learn.</p>
<p>If you want to return a single result array instead of a cursor, use the <a href="http://php.net/manual/en/mongocollection.findone.php">findOne()</a> method.</p>
<h3>Inserting a new document</h3>
<p>Inserting a new row/document is really easy. Since the collection (and all of MongoDB for that matter) doesn't have any schema or structure to enforce, you can insert any array into any collection, so long as it's a valid array.</p>
```php
$collection->insert(array('name' => 'bob','age' => 40 ));
$collection->insert(array('someUnrelatedKey' => 'someUnrelatedValue'));
```
<p>You can also inserted nested arrays (which will become nested objects in JSON):</p>
```php
$collection->insert(array('name' => 'bob','age' => 40,
  'computers' =>(array('laptop','desktop')) ));
```
<h3>Updating an existing document</h3>
<p>Updating a document is where MongoDB shows it's flexibility as a NoSQL database. It's also the trickiest of the CRUD operations. The basic structure with psuedo-code is as follows:</p>
```php
$collection->update(search_criteria, 
  array('$set' => array("key => "value") ));
```
<p>Here's an example with legitimate values:</p>
```php
$collection->update(array("name" => "alex"), 
  array('$set' => array("name" => "bob")));
```
<p>This query is equivalent to "UPDATE collection SET name='bob' where name='alex';" in traditional SQL.</p>
<p>Notice the $set argument - without this, MongoDB will REPLACE the entire document with the array you passed instead of trying to update a particular element. There are several <a href="http://docs.mongodb.org/manual/reference/operator/update/">update operators for MongoDB</a> that can be very helpful. For example, let's assume we had the following document object:</p>
```javascript
{ "name": "bob", "age": 35, "computers": ["laptop","desktop"] }
```
<p>Notice that we have a nested array of "computers" with two values. If we wanted to add another element to the "computers" array, we'd have to fetch the entire document, strip out the nested array object, add our new element, then use the $set operator to update the array in the document.</p>
<p>Fortunately, MongoDB provides a special update operator for this use case, $push. Let's pretend we want to update bob's record in the database and give him a VM.</p>
```php
$collection->update(array('name' => 'bob'),
  array('$push' => array('computers' => 'vm')));
```
<p>Awesome.</p>
<p>This was a pretty elementary tutorial on MongoDB, but I'm hoping it either helped one of you get started or inspired someone to use MongoDB in their project. Everything I covered in this post and much more can be found by perusing the <a href="http://php.net/manual/en/book.mongo.php" target="_blank">Mongo Book at php.net</a> and <a href="http://docs.mongodb.org/manual/reference/" target="_blank">MongoDB's Reference</a>.</p>
