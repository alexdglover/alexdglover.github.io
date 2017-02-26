---
title: Share Your Salary App Part 1
date: 2015-01-23 09:30:51.000000000 -06:00
categories:
- IT/Software Projects
- How-to Guides
tags:
- f3
- fat free framework
- mongodb
- php
- swagger ui
- rest
- restful
- api
---
<p>In a previous project, I built "<a href="http://alexdglover.com/whos-that-person-in-that-thing-app/" target="_blank">Who's that person in that thing?</a>," a JavaScript web app that called The Movie DB's API to find common actors given two movies. That was fun, but I still wanted to build an API service myself and I wanted to experiment with some new tools. But first I needed a project idea as an excuse to use them.</p>
<h2>Project Idea/Application Premise/The Excuse</h2>
<p>People are funny about money, especially about their salary. With most people, it's a taboo to talk about it. But everyone's been curious at some point to know how they compare to their peers. Some people fear they are getting paid less to do the same work as others, and sometimes that fear is founded in fact. The "Share Your Salary" app is an anonymous salary survey tool to address these kinds of use cases.</p>
<p>Before we get into any of the tools or how-to, feel free to check out the application first or look over the source.</p>

[Demo](http://shareyoursalary-alexdglover.rhcloud.com){: .btn .btn--large .btn--info}
[GitHub Repo](https://github.com/alexdglover/shareyoursalary){: .btn .btn--large .btn--info}
{: .text-center}

<h2>New Tools</h2>
<h3><a title="OpenShift.com" href="http://www.openshift.com" target="_blank" rel="nofollow">OpenShift</a></h3>
<p>Well I started to write this section, turned out to be about a thousand words... so I moved it to <a title="Introduction to OpenShift" href="http://alexdglover.com/introduction-to-openshift/" target="_blank">it's own separate post</a>. In short, OpenShift is a PaaS hosting service. OpenShift hosts the entire Share Your Salary app, including a MongoDB container, an HAProxy load balancer, and Apache/PHP containers in an auto-scaling group.</p>
<h3><a title="MongoDB.org" href="http://www.mongodb.org/" target="_blank">MongoDB</a></h3>
<p>MongoDB is a highly scalable NoSQL database. MongoDB is a document database and uses JavaScript Object Notation (JSON) to represent those documents. I really like JSON, but PHP doesn't 'speak' JSON natively, which makes using MongoDB a little less intuitive. I wrote <a title="MongoDB and PHP Primer" href="http://alexdglover.com/mongodb-and-php-primer/" target="_blank">a separate MongoDB and PHP Primer post</a> to go over some of the basics.</p>
<p>I used MongoDB as the backend for the "Share Your Salary" app.</p>
<h3><a title="FatFreeFramework.com" href="http://fatfreeframework.com/" target="_blank">Fat Free Framework</a></h3>
<p>Fat Free Framework (or F3 for short) is a tiny PHP framework (it's only ~65 KB in total) that provides URL routing, caching, page templating, and built-in database support for MySQL, SQLite, MSSQL/Sybase, PostgreSQL, MongoDB and F3's proprietary flat-file DB called Jig.</p>
<p>I used F3 for both templating web front-end as well as the API.</p>
<h3><a title="Swagger.IO" href="http://swagger.io/" target="_blank">Swagger UI</a></h3>
<p>Swagger UI is a must if you are building a RESTful API service - it acts as both a documentation method and a test bed. As the developer, you complete a JSON file that describes your API routes, methods, parameters, etc. Swagger UI transforms that JSON into a web page and API client that uses JavaScript to send HTTP GET/POST/PUT/DELETE requests to your API service and displays the results. Check out the <a title="petstore.swagger.wordnik.com" href="http://petstore.swagger.wordnik.com/" target="_blank">Swagger Petstore Demo</a>.</p>
<p><!--more--></p>
<h3>Retrospective</h3>
<p>I would normally title this section "How-to" or something along those lines, but trying to remember all of the steps I went through after the fact is difficult. I tend to get caught up in a project and document afterwards (shame on me).</p>
<p>That said, I'll try to break down the highlights and lessons learned from this project.</p>
<h5>The 'Database' Class</h5>
<p>A quick disclaimer - I have a hard time thinking in object-oriented terms, I generally write functional programs. I tried to push myself to write more object-oriented with some simple classes to represent objects but I'm sure I botched it. Any criticisms or corrections are welcome.</p>
<p>Now, with any good OO program, you'll want to create a database class that can be instantiated by other classes. This allows you to write the basic database code once and re-use it elsewhere, instead of having a separate set of database connection code in every class that needs it.</p>
<p>Let's go over the code with some extra comments:</p>
```php
<?php

// Here we are defining a constant variable, OPENSHIFT_DB with the name
// of the MongoDB database we'll be connecting to, 'shareyoursalary'
define("OPENSHIFT_DB", "shareyoursalary");

class Database{

  // Here we define the constructor that will be called each time the
  // Database class is instantiated. In this case, we're just going
  // to call the get_db_connection() function
  function __construct(){
    $this->get_db_connection();
  }

  // Since I used OpenShift for this project, all of the database
  // connection information was abstracted to environment variables.
  // Ultimately, we are constructing a MongoClient class object and
  // returning it to whatever called this function
  function get_db_connection() {
    $host = $_ENV["OPENSHIFT_MONGODB_DB_HOST"];
    $user = $_ENV["OPENSHIFT_MONGODB_DB_USERNAME"];
    $passwd = $_ENV["OPENSHIFT_MONGODB_DB_PASSWORD"];
    $port = $_ENV["OPENSHIFT_MONGODB_DB_PORT"];
    $uri = "mongodb://" . $user . ":" . $passwd . "@" . $host . ":" . $port;
    $mongo = new MongoClient($uri);
    return $mongo;
  }

  // Returns a database object based on a database name input parameter ($dbname)
  function get_database($dbname) {
    $conn = $this->get_db_connection();
    return $conn->$dbname;
  }

  // Returns a collection object based on a collection name input ($collection)
  function get_collection($collection) {
    $db = $this->get_database(OPENSHIFT_DB);
    return $db->$collection;
  }

}
?>
```
<p>This class is pretty straightforward and does little in the way of error handling. Basically we're just connecting to the database and abstracting some basic MongoDB functions to make them available as class functions.</p>
<h5>The 'Survey' Class</h5>
<p>The Survey object is the only real object in this program from an object-oriented programming perspective. This object (the survey) is the core of the application, and so most of the relevant data exists as properties of this class.</p>
<p>Let's walk through the source with some comments:</p>
```php
<?php

class Survey {

// Class properties are defined here. These should probably be all marked as private varaiables

  var $surveyName; // Human-readable survey name
  var $URLName; // URL-friendly version of the surveyName property
  var $currency; // the currency that this survey is based on, e.g. USD
  var $period; // The period that the survey is based on, e.g. per hour or per year
  var $minentries; // The minimum number of entries needed before the results are published. This ensures anonymity
  private $db; // Property to hold the Database object

  // This is the constructor function. It instantiates the database class and sets it to the local db property
  public function __construct(){
    $this->db = new Database();
  }

  // Simple getter functions for each of the properties. These aren't actually used anywhere, but I created them as a standard practice
  public function get_name(){
    return $this->surveyName;
  }

  public function get_url_name(){
    return $this->URLName;
  }

  public function get_currency(){
      return $this->currency;
  }

  public function get_period(){
    return $this->period;
  }

  public function get_minentries(){
    return $this->minentries;
  }

  // This function will take the human-readable name as an input, then convert and return the URL-friendly version
  private function convertNameToURLName($string) {
    //Lower case everything
    $string = strtolower($string);
    //Make alphanumeric (removes all other characters)
    $string = preg_replace("/[^a-z0-9_\s-]/", "", $string);
    //Clean up multiple dashes or whitespaces
    $string = preg_replace("/[\s-]+/", " ", $string);
    //Convert whitespaces and underscore to dash
    $string = preg_replace("/[\s_]/", "-", $string);
    return $string;
  }


  // This function finds a given survey in the database based on the surveyName
  // passed as an input parameter. The surveyName will be sent as part of
  // the $args parameter (sent as part of a URL path parameter via F3).
  // If found, the survey will be echo'd in JSON format (this
  // allows it to be consumed as an API)
  public function getByName($f3,$args) {
    $surveys = $this->db->get_collection('surveys');

    // Convert name to URL friendly name
    $URLName = $this->convertNameToURLName($args['name']);

    $query = array('URLName' => $URLName);
    $cursor = $surveys->find($query);
    foreach ($cursor as $doc){
      if(count($doc['responses']) < $doc['minEntries'])
      {
        for($i=0; $i < (count($doc['responses'])); $i++)
        {
          $doc['responses'][$i] = '0';
        }
        echo json_encode($doc);
      }
      else {
        echo json_encode($doc);
      }
    }
  }

  // The addSurvey function takes HTTP parameters (the $_REQUEST variables) as
  // inputs and creates the new survey entry in the database. Once created, the
  // function attempts to find the survey and echo it in JSON format (this allows it to be consumed as an API)
  public function addSurvey($f3,$args) {
    // Get surveys collection    
    $surveys  = $this->db->get_collection('surveys');
    $surveyName   = $_REQUEST['surveyName'];
    // Convert name to URL friendly name
    $URLName = $this->convertNameToURLName($surveyName);
    $currency  = $_REQUEST['currency'];
    $period    = $_REQUEST['period'];
    $minEntries   = $_REQUEST['minEntries'];

    // Insert new data sent via API call
    $surveys->insert(array('name' => $surveyName, 'URLName' => $URLName, 'currency' => $currency, 'period'=>$period, 'minEntries'=>$minEntries, 'responses'=>(array())));
    // Build a query object, basically a single item array with the name of the new survey input value
    $query = array('URLName' => $URLName);
    // Attempt to find the survey we just created
    $cursor = $surveys->find($query);
    // For each object that matches the query, echo the data as JSON
    foreach ($cursor as $doc){
      echo json_encode($doc);
    }
  }

  // The addResponse function takes a survey name (as a URL parameter passed via F3)
  // and a response input (via HTTP query parameters, the $_REQUEST variable below).
  // It uses the survey name to find the survey in the database and then updates the
  // embedded response array with the response value sent. Once updated, the function
  // returns the newly updated survey in JSON format
  public function addResponse($f3,$args) {
    $surveys = $this->db->get_collection('surveys');
    $response = $_REQUEST["response"];

    // Convert name to URL friendly name
    $URLName = $this->convertNameToURLName($args['name']);

    $surveys->update(array('URLName' => $URLName),array('$push' => array('responses' => $response)));
    $query = array('URLName' => $URLName);
    $cursor = $surveys->find($query);
    foreach ($cursor as $doc){
        echo json_encode($doc);
    }
  }
}
?>
```
<p>Looking back, this Survey class contains aspects of both a model and a controller. In a proper MVC project, this probably should have been split into two classes, a 'Survey' object (with just the basic properties and getters/setters) and a 'SurveyController' (with the addSurvey and addResponse).</p>
<p>These classes might seem a bit abstract right now, but it'll make more sense once I explain the API routes in F3.</p>
<p>At 1400+ words, this is a good place to take a break. Check out Part 2 for the PageGenerator class, the F3 routes, and a how-to for Swagger UI.</p>
