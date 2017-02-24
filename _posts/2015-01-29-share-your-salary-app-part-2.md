---
title: Share Your Salary App Part 2
date: 2015-01-29 09:45:59.000000000 -06:00
categories:
- Fun IT Projects
- How-to Guides
tags:
- api
- f3
- mongodb
- php
- swagger ui
header:
  teaser: /assets/SwaggerUILogo.png
---
<p>In <a title="Share Your Salary App Part 1" href="http://alexdglover.com/share-your-salary-app-part-1/" target="_blank">Part 1</a>, I covered the core classes for the Share Your Salary application. These classes covered my Model and Controllers in my quasi-MVC application. Now we're ready to expose those controller functions via a RESTful API.</p>
<p>For this project, I used the Fat Free Framework for both API routes and web page templating. To start, I simply downloaded and unzipped the <a href="https://github.com/bcosca/fatfree/archive/master.zip">F3 package</a> and added the contents to my shareyoursalary app directory. Next, I created a "classes" folder and added the PHP class files I created in Part 1. At this point, my folder structure looked like this:</p>
<ul>
<li>shareyoursalary/
<ul>
<li>classes/
<ul>
<li>database.php</li>
<li>survey.php</li>
</ul>
</li>
<li>config.ini</li>
<li>lib/</li>
<li>.htaccess</li>
<li>index.php</li>
<li>readme.md</li>
<li>ui/
<ul>
<li>css/</li>
<li>images/</li>
<li>layout.htm</li>
<li>userref.htm</li>
<li>welcome.htm</li>
</ul>
</li>
</ul>
</li>
</ul>
<p>Now to create the API functionality, I created <a href="http://fatfreeframework.com/routing-engine" target="_blank">routes in F3</a>. F3 makes it really easy to make HTTP method-specific routes that then map to pages, classes, or specific functions. In my case, I wanted to create routes for the various functions I created in the Survey class. To do this, we'll need to edit the index.php file in the root of the F3 directory. I left the existing code as-is, and added the following lines:</p>
```php
$f3->route('GET /api/v1/survey/getByName/@name','Survey->getByName');
$f3->route('POST /api/v1/survey/addResponse/@name','Survey->addResponse');
$f3->route('POST /api/v1/survey/addSurvey','Survey->addSurvey');
```
<p>As you can see, the route syntax is:<br />
'METHOD /URL/PATH/@parameter', 'Class->functionName'.</p>
<p>RESTful APIs should use certain methods based on what you're doing to the object (e.g. if you're reading an object, use GET, if you're creating an object, use POST). Here's a quick breakdown for reference:</p>
<p>GET - Reading an object<br />
POST - Creating a new object (or overwriting an entire object)<br />
PUT - Updating attributes of an existing object (this should be an <a href="http://en.wikipedia.org/wiki/Idempotence" target="_blank">idempotent </a>operation)<br />
DELETE - I'll give you three guesses.</p>
<p>There are other methods, but these are the four main methods you'll encounter with APIs.</p>
<p><!--more--></p>
<p>Back to the F3 routes - you'll notice the @parameters in the URL. This tells F3 to pick up this portion of the URL and set it as a variable. You can then reference these parameters/variables in templates:</p>
```php
echo $f3->get('PARAMS.parameterName');
```
<p>or you can use them inside of your classes/functions:</p>
```php
// Remember the route from index.php:
// $f3->route('GET /api/v1/survey/getByName/@name','Survey->getByName');
public function getByName($f3,$args) {
...
 $URLName = $this->convertNameToURLName($args['name']);
...
```
<p>OK, so now I can hit <a href="http://shareyoursalary-alexdglover.rhcloud.com/api/v1/survey/getByName/test" target="_blank">http://shareyoursalary-alexdglover.rhcloud.com/api/v1/survey/getByName/test</a> in a browser, and I'll get some JSON output in response (unless 'test' doesn't exist, in which case I'll get an HTTP 200 OK response but no output).</p>
<p>What about the other two routes? If you try to hit them in a browser, you'll get a nasty HTTP 405 Error - Method Not Allowed. Remember, a browser by default is GETting web pages, and F3 enforces the HTTP method you've assigned. So how are we going to test our API routes? We could install some browser plugin or execute cURL commands against it... but let's do something friendly for our API consumers, let's make it easy for people to explore the API. Enter Swagger UI.</p>

<p style="text-align: center;"><a href="http://swagger.io/"><img class="aligncenter size-full wp-image-888" src="{{ "/assets/SwaggerUILogo.png" | absolute_url }}" alt="SwaggerUILogo" width="567" height="204" /></a></p>

<p>I already gave an overview of Swagger UI in <a title="Share Your Salary App Part 1" href="http://alexdglover.com/share-your-salary-app-part-1/" target="_blank">Part 1</a>, now for the implementation details. To start, I downloaded the <a href="https://github.com/swagger-api/swagger-ui/archive/master.zip">Swagger UI package</a>, unzipped it the root of the project directory and renamed the directory 'swagger' leaving our new file structure like this:</p>
<ul>
<li>shareyoursalary/
<ul>
<li>classes/
<ul>
<li>database.php</li>
<li>survey.php</li>
</ul>
</li>
<li>config.ini</li>
<li>lib/</li>
<li>.htaccess</li>
<li>index.php</li>
<li>swagger/
<ul>
<li>bin/</li>
<li>dist/</li>
<li>lib/</li>
<li>src/</li>
<li>...</li>
</ul>
</li>
<li>readme.md</li>
<li>ui/
<ul>
<li>css/</li>
<li>images/</li>
<li>layout.htm</li>
<li>userref.htm</li>
<li>welcome.htm</li>
</ul>
</li>
</ul>
</li>
</ul>
<p>Since swagger won't be managed by F3's template engine, I kept it outside of the ui directory. To make the Swagger web pages available, I add a new route in the F3 index.php file:</p>
```php
route('GET /swagger/',
  function($f3) {
    echo F3::serve('/swagger/dist/index.html');
  }
);
```
<p>Now we can browse to /swagger/dist/index.html and we should see the Swagger UI page with the PetStore example. We're off to a good start.</p>
<p>Next, we need to modify /swagger/dist/index.html to refer to our new API JSON document (which we haven't created yet). On line 31, we need to provide the full URL path to the JSON file:</p>

```javascript
url = window.location.protocol + "//" + window.location.host + "/swagger/dist/shareyoursalary.json";
```

<p>Note that this URL variable will work regardless of what DNS name the app is using (more on this later). Alright, now we'll create the shareyoursalary.json file to describe the API. Let's take a look at a few snippets and then break each one down:</p>

```javascript
{
"swagger": "2.0",
"info": {
"version": "0.0.1",
"title": "Share Your Salary"
},
"basePath": "/api/v1",
...
```
<p>Pretty easy right? Just providing some version numbers, a title, and a base path. The base path is the most important piece here - this will be pre-pended to all of the other API URLs/path. Next, let's take a look at the first path:</p>

```javascript
"paths": {
  "/survey/getByName/{name}": {
    "get": {
      "description": "Gets `Survey` objects.\nRequired query param of **name** will be used as search criteria.\nShould only return one object.\nIf minimum entries haven't been met, responses will not be included in return data.\n",
      "parameters": [
        {
          "name": "name",
          "in": "path",
          "description": "name of survey",
          "required": true,
          "type": "string",
          "format": "string"
        }
      ],
      "responses": {
        "200": {
          "description": "Successful response",
          "schema": {
            "title": "SurveyJSON",
            "type": "array",
            "items": {
              "title": "Survey",
              "type": "object",
              "properties": {
                "name": {
                  "type": "string"
                },
                "currency": {
                  "type": "string"
                },
                "period": {
                  "type": "string"
                },
                "minEntries": {
                  "type": "number"
                }
              }
            }
          }
        }
      }
    }
  }
}
...
```
<p>Each path is identified by a key, which is the actual URL/path ("/survey/getByName/{name}" in this example). Each URL can have multiple functions based on what HTTP method is used, so we'll provide a description, parameters, and a response definition for each method. In this example, we are only dealing with GET. The parameter attributes are fairly straightforward except for the "in" attribute - the "in" attribute defines how the parameters are passed to this API. APIs can have parameter values passed in one of two ways:</p>
<ol>
<li>As part of the URL <strong>path</strong> (e.g. /object/function/value)</li>
<li>As a <strong>query</strong> parameter (e.g. /object/function?parameter=value)</li>
</ol>
<p>When passing a path parameter, we also need to add it to the routes inside of {curly braces}, like /survey/getByName/{name}. Finally, we define an expected response, which is straightforward. My entire "shareyoursalary.json" file is available on <a href="https://github.com/alexdglover/shareyoursalary/blob/master/swagger/dist/shareyoursalary.json" target="_blank">GitHub </a>if you would like more example code. Now that we completed our JSON document describing our API, we can simply browse to the Swagger UI page and see all of the documentation as well as a working API client. Take a minute to check it out and fire off some API requests: <a href="http://shareyoursalary-alexdglover.rhcloud.com/swagger/dist/index.html" target="_blank">http://shareyoursalary-alexdglover.rhcloud.com/swagger/dist/index.html</a>.</p>
<p>Let's switch gears for a second, move away from the technical how-to and go over a few API philosophies and best practices.</p>
<ol>
<li>Include a version in your API paths (you'll notice I used /api/v1/ in my base path). If you ever need to change the API in some way that's not backward compatible, you'll want to create a separate API route for the new version. This allows you to host two APIs, giving your downstream users the chance to re-write their code and switch to your new API before you kill the previous version.</li>
<li>Use the built-in HTTP methods and responses appropriately. This will make your API easier for you and your users to consume and debug. For example, throw an HTTP 405 error if someone sends a POST request to an API that only allows GET. If you threw an HTTP 500 error instead, people will wonder if there's a runtime error on the server rather than a mistake on their end. On that note, use HTTP 500 sparingly - this should only be thrown if you can't catch/handle an error appropriately, or it should be accompanied with some useful error text.</li>
<li>Make your API fun and easy to use. You want to encourage exploration and experimentation so other developers will build awesome applications on top of your already awesome API. You can accomplish this by using tools like Swagger and adding related calls to your API responses (i.e. if someone hits /invoice/123, send a "related objects" attribute in the response with routes to /invoice/122, /invoice/124, and /purchaseOrder/123 - that way the the user is encouraged to traverse or "walk" your API)</li>
</ol>
<p>Awesome - now we have a working API service, our API is documented, and we can test it directly in case our AJAX-based UI has issues later.</p>
<p>In Part 3, we'll build a front-end (using jQuery, Bootstrap, F3, and Google Charts) that consumes our API service, set up DNS names, and review the application overall.</p>
