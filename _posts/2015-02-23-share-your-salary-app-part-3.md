---

title: Share Your Salary App Part 3
date: 2015-02-23 10:30:16.000000000 -06:00



categories:
- Fun IT Projects
- How-to Guides
tags:
- api
- bootstrap
- fat free framework
- jQuery
- MVC
- openshift
- php
- rest
- restful
meta:
  _edit_last: '1'
  _s2mail: 'yes'
  _publicize_twitter_user: "@alexdglover"
  _wpas_done_all: '1'
  _thumbnail_id: '914'
  _wpas_skip_1477652: '1'
  _wpas_skip_1477650: '1'
---
<p>So in <a title="Share Your Salary App Part 1" href="http://alexdglover.com/share-your-salary-app-part-1/" target="_blank">Part 1</a> we built the core classes for our app and we exposed those classes as a RESTful web service in <a title="Share Your Salary App Part 2" href="http://alexdglover.com/share-your-salary-app-part-2/" target="_blank">Part 2</a>. Now, we'll build a UI and wrap up the project.</p>
<p>Feel free to check out the application first or pull up the source in another window.</p>
<p style="text-align: center;"><a href="http://shareyoursalary-alexdglover.rhcloud.com"><input class="button" type="button" value="Demo" /></a> <a href="https://github.com/alexdglover/shareyoursalary"><input class="button" type="button" value="Source on GitHub" /></a></p>
<p>The Fat Free Framework configuration allows you to specify a document root for the front-end pages. By default, this is set to the 'ui' directory in the root of the F3 directory structure. This will be our working directory for building the UI. The F3 package will have a few pages, CSS, and images in the 'ui' directory, you can delete them all if you want (I kept the welcome.htm file just out of convenience).</p>
<p>Now I'm not a good UI developer, so I tend to use the <a href="http://getbootstrap.com/2.3.2/" target="_blank">Bootstrap framework (v2.3.2)</a> for the front-end of my applications. Go ahead and download the Bootstrap package and unzip it in the 'ui' directory. The 'ui' directory structure should look like this:</p>
<ul>
<li>ui/
<ul>
<li>js/
<ul>
<li>bootstrap.js</li>
<li>bootstrap.min.js</li>
<li>jquery.js</li>
</ul>
</li>
<li>css/
<ul>
<li>base.css</li>
<li>bootstrap-responsive.css</li>
<li>bootstrap-responsive.min.css</li>
<li>bootstrap.css</li>
<li>bootstrap.min.css</li>
<li>theme.css</li>
</ul>
</li>
<li>img/
<ul>
<li>glyphicons-halflings-white.png</li>
<li>glyphicons-halflings.png</li>
</ul>
</li>
<li>welcome.htm (from the f3 package)</li>
</ul>
</li>
</ul>
<p>Now we have the components that we need to start building pages. Let's start by building a layout.htm file - this will be the base HTML file (with a common header, footer, styles and script tags) that will be populated with content for each page. These types of pages/files are referred to as templates.</p>
<p><!--more--></p>
<p>Again... I'm a terrible UI developer so I blatantly 'borrowed' the majority of this page from one of the <a href="http://getbootstrap.com/2.3.2/examples/justified-nav.html" target="_blank">Bootstrap examples</a>.</p>
<pre class="lang:default decode:true">&lt;!DOCTYPE html&gt;
&lt;html lang="en"&gt;
  &lt;head&gt;
    &lt;meta charset="utf-8"&gt;
    &lt;title&gt;ShareYourSalary.Info&lt;/title&gt;
    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt;
    &lt;meta name="description" content=""&gt;
    &lt;meta name="author" content=""&gt;
    &lt;script src="/ui/js/jquery.js"&gt;&lt;/script&gt;
    &lt;script src="/ui/js/bootstrap.js"&gt;&lt;/script&gt;
    &lt;script type="text/javascript"&gt;
      $(document).ready(function(){
        $('[data-toggle="popover"]').popover({
	  placement : 'top'
        });
      });
    &lt;/script&gt;

    &lt;!-- Le styles --&gt;
    &lt;link href="/ui/css/bootstrap.css" rel="stylesheet"&gt;
    &lt;style type="text/css"&gt;
      body {
        padding-top: 20px;
        padding-bottom: 60px;
      }
      /* Custom container */
      .container {
        margin: 0 auto;
        max-width: 1000px;
      }
      .container &gt; hr {
        margin: 60px 0;
      }
      /* Main marketing message and sign up button */
      .jumbotron {
        margin: 80px 0;
        text-align: center;
      }
      .jumbotron h1 {
        font-size: 100px;
        line-height: 1;
      }
      .jumbotron .lead {
        font-size: 24px;
        line-height: 1.25;
      }
      .jumbotron .btn {
        font-size: 21px;
        padding: 14px 24px;
      }
      /* Supporting marketing content */
      .marketing {
        margin: 60px 0;
      }
      .marketing p + h4 {
        margin-top: 28px;
      }
      /* Customize the navbar links to be fill the entire space of the .navbar */
      .navbar .navbar-inner {
        padding: 0;
      }
      .navbar .nav {
        margin: 0;
        display: table;
        width: 100%;
      }
      .navbar .nav li {
        display: table-cell;
        width: 1%;
        float: none;
      }
      .navbar .nav li a {
        font-weight: bold;
        text-align: center;
        border-left: 1px solid rgba(255,255,255,.75);
        border-right: 1px solid rgba(0,0,0,.1);
      }
      .navbar .nav li:first-child a {
        border-left: 0;
        border-radius: 3px 0 0 3px;
      }
      .navbar .nav li:last-child a {
        border-right: 0;
        border-radius: 0 3px 3px 0;
      }
    &lt;/style&gt;
    &lt;link href="/ui/css/bootstrap-responsive.css" rel="stylesheet"&gt;

    &lt;!-- HTML5 shim, for IE6-8 support of HTML5 elements --&gt;
    &lt;!--[if lt IE 9]&gt;
      &lt;script src="ui/js/html5shiv.js"&gt;&lt;/script&gt;
    &lt;![endif]--&gt;

    &lt;!-- Fav and touch icons --&gt;
    &lt;link rel="apple-touch-icon-precomposed" sizes="144x144" href="/ui/ico/apple-touch-icon-144-precomposed.png"&gt;
    &lt;link rel="apple-touch-icon-precomposed" sizes="114x114" href="/ui/ico/apple-touch-icon-114-precomposed.png"&gt;
      &lt;link rel="apple-touch-icon-precomposed" sizes="72x72" href="/ui/ico/apple-touch-icon-72-precomposed.png"&gt;
                    &lt;link rel="apple-touch-icon-precomposed" href="/ui/ico/apple-touch-icon-57-precomposed.png"&gt;
                                   &lt;link rel="shortcut icon" href="/ui/ico/favicon.png"&gt;
  &lt;/head&gt;

  &lt;body&gt;

    &lt;div class="container"&gt;

      &lt;div class="masthead"&gt;
        &lt;h1 class="muted"&gt;ShareYourSalary&lt;/h1&gt;
        &lt;div class="navbar"&gt;
          &lt;div class="navbar-inner"&gt;
            &lt;div class="container"&gt;
              &lt;ul class="nav"&gt;
                &lt;li class="active"&gt;&lt;a href="/"&gt;Home&lt;/a&gt;&lt;/li&gt;
                &lt;li&gt;&lt;a href="/swagger/dist/index.html"&gt;API Documentation&lt;/a&gt;&lt;/li&gt;
                &lt;li&gt;&lt;a href="https://github.com/alexdglover/shareyoursalary"&gt;Github Project&lt;/a&gt;&lt;/li&gt;
              &lt;/ul&gt;
            &lt;/div&gt;
          &lt;/div&gt;
        &lt;/div&gt;&lt;!-- /.navbar --&gt;
      &lt;/div&gt;

	&lt;!-- Put content here --&gt;
	&lt;?php echo $this-&gt;render(Base::instance()-&gt;get('content')); ?&gt;

      &lt;hr&gt;

      &lt;div class="footer"&gt;
        &lt;p&gt;&amp;copy; alexdglover 2014&lt;/p&gt;
      &lt;/div&gt;

    &lt;/div&gt; &lt;!-- /container --&gt;

    &lt;!-- Le javascript
    ================================================== --&gt;
    &lt;!-- Placed at the end of the document so the pages load faster --&gt;
    &lt;script src="/ui/js/jquery.js"&gt;&lt;/script&gt;
    &lt;script src="/ui/js/bootstrap.js"&gt;&lt;/script&gt;
  &lt;/body&gt;
&lt;/html&gt;</pre>
<p>Now the important piece we've added here is between the 'masthead' and 'footer' divs:</p>
<pre class="lang:default decode:true ">&lt;!-- Put content here --&gt;
&lt;?php echo $this-&gt;render(Base::instance()-&gt;get('content')); ?&gt;</pre>
<p>Now we can just set the 'content' variable in our F3 code to populate the body of the template and we will get consistent and clean looking pages. That's the whole purpose of templates and template engines - build a solid base page (or pages) and just populate the contents dynamically. This makes for great code re-use and you don't have to propagate changes across dozens of files.</p>
<h3>Static Pages</h3>
<p>For populating the content with a static page, we don't really need a Controller class, we just need F3's routing functionality to set the 'content' variable to a static HTML file. The ShareYourSalary app home page is going to be static, so let's use that as an example.</p>
<p>For the home page, we'll want a simple explanation of what the site/app does and an entry form for users to create a new survey. Our entry form will POST values to the API using jQuery and route us to a result page if it's successful. Remember, we don't have to worry about JavaScript or CSS references because our layout.htm already includes all of that. Let's take a look at the home page (welcome.htm):</p>
<pre class="lang:default decode:true" title="welcome.htm">&lt;div class="row"&gt;
        &lt;div class="span5"&gt;
		&lt;h2&gt;Share your salary!&lt;/h2&gt;
		&lt;p&gt;Have you ever worried that your colleagues, doing the same job, make more money than you? According to a recent Glassdoor survey, 39% of Americans feel they're underpaid.&lt;/p&gt;
		&lt;p&gt;Or maybe you're curious how you rank compared to your high school class?&lt;/p&gt;
		&lt;p&gt;Maybe someone on your team is getting underpaid, but you're not comfortable talking about salaries.&lt;/p&gt;
		&lt;p&gt;Then create a free, anonymous salary survey!&lt;/p&gt;
	&lt;/div&gt;
	&lt;div class="span5"&gt;
		&lt;form&gt;
			&lt;fieldset&gt;
			&lt;legend&gt;Create a survey&lt;/legend&gt;
			&lt;label&gt;Survey Name&lt;/label&gt;	
			&lt;input class="input-xlarge" name="surveyName" type="text" placeholder="Employer, team name, or totally arbitratry" /&gt;
			&lt;label&gt;Currency&lt;/label&gt;
			&lt;select class="input-medium" name="currency"&gt;
				&lt;option value="" disabled=""&gt;Select Currency&lt;/option&gt;
				&lt;option value="AUD"&gt;Australian Dollar&lt;/option&gt;
				&lt;option value="BRL"&gt;Brazilian Real &lt;/option&gt;
				&lt;option value="CAD"&gt;Canadian Dollar&lt;/option&gt;
				&lt;option value="CZK"&gt;Czech Koruna&lt;/option&gt;
				&lt;option value="DKK"&gt;Danish Krone&lt;/option&gt;
				&lt;option value="EUR"&gt;Euro&lt;/option&gt;
				&lt;option value="HKD"&gt;Hong Kong Dollar&lt;/option&gt;
				&lt;option value="HUF"&gt;Hungarian Forint &lt;/option&gt;
				&lt;option value="ILS"&gt;Israeli New Sheqel&lt;/option&gt;
				&lt;option value="JPY"&gt;Japanese Yen&lt;/option&gt;
				&lt;option value="MYR"&gt;Malaysian Ringgit&lt;/option&gt;
				&lt;option value="MXN"&gt;Mexican Peso&lt;/option&gt;
				&lt;option value="NOK"&gt;Norwegian Krone&lt;/option&gt;
				&lt;option value="NZD"&gt;New Zealand Dollar&lt;/option&gt;
				&lt;option value="PHP"&gt;Philippine Peso&lt;/option&gt;
				&lt;option value="PLN"&gt;Polish Zloty&lt;/option&gt;
				&lt;option value="GBP"&gt;Pound Sterling&lt;/option&gt;
				&lt;option value="SGD"&gt;Singapore Dollar&lt;/option&gt;
				&lt;option value="SEK"&gt;Swedish Krona&lt;/option&gt;
				&lt;option value="CHF"&gt;Swiss Franc&lt;/option&gt;
				&lt;option value="TWD"&gt;Taiwan New Dollar&lt;/option&gt;
				&lt;option value="THB"&gt;Thai Baht&lt;/option&gt;
				&lt;option value="TRY"&gt;Turkish Lira&lt;/option&gt;
				&lt;option value="USD" selected="YES"&gt;U.S. Dollar&lt;/option&gt;
			&lt;/select&gt;
			&lt;label&gt;Period&lt;/label&gt;
		        &lt;select class="input-medium" name="period"&gt;
	                	&lt;option value="Per Year"&gt;Per Year&lt;/option&gt;
        	        	&lt;option value="Per Hour"&gt;Per Hour&lt;/option&gt;
	        	&lt;/select&gt;
			&lt;label&gt;Minimum number of responses &lt;i class="icon-question-sign" data-toggle="popover" data-placement="top" title="Miniumum number of responses" data-content="In order to ensure anonymity, salary survey results won't be published until a minimum number of salary responses are submitted. Set this to '0' if you don't care about anonymity."&gt;&lt;/i&gt;&lt;/label&gt;
			&lt;input class="input-mini" name="minEntries" type="number" value=5 /&gt;
			&lt;p&gt;&lt;input id="submit" type="submit" class="btn btn-success" value="Submit" /&gt;&lt;/p&gt;
		&lt;/form&gt;
	&lt;/div&gt;
&lt;/div&gt;
&lt;script&gt;
	$( "form" ).on( "submit", function( event ) {
		event.preventDefault();
		var datastring = $( "form" ).serialize();
		$.ajax({
		    type: "POST",
		    url: "/api/v1/survey/AddSurvey",
		    data: datastring,
		    dataType: "json",
		    success: function(data) {
		    	window.location.href = "/thanks/" + data.URLName;
		    },
		    error: function(){
		          alert('Oops... something went wrong. Please try again.');
		    }
		});		
	});
&lt;/script&gt;</pre>
<p>Perfect. The default index.php that's included in the F3 package will include a route for "GET /" that we can use. This section should already exist in the index.php if you're using the default file that came with the F3 zip file, but just confirm that these lines exist in your index.php:</p>
<pre class="lang:default decode:true ">$f3-&gt;route('GET /',
	function($f3) {
            ...
            // lots of default F3 code
            ...
            $f3-&gt;set('content','welcome.htm');
            echo View::instance()-&gt;render('layout.htm');
	}
);</pre>
<p>If you want to use a different file name for your home page (instead of welcome.htm) just change it in this line of the index.php file. Now if we browse to our site, we should see our home page:</p>
<p><a href="http://alexdglover.com/wp-content/uploads/2015/02/ShareYourSalary-HomePage.png"><img class="aligncenter size-large wp-image-914" src="{{ site.baseurl }}/assets/ShareYourSalary-HomePage-1024x654.png" alt="ShareYourSalary Home Page" width="860" height="549" /></a></p>
<p>Now we can partially test this form if we want. If we submit a survey and the API processes it successfully, we'll get routed to a page that doesn't exist yet (that's OK, that's expected behavior). But how do we know the submission worked? We can go back to our API test page we created in <a title="Share Your Salary App Part 2" href="http://alexdglover.com/share-your-salary-app-part-2/">Part 2</a> and execute a getByName API call.</p>
<p><a href="http://alexdglover.com/wp-content/uploads/2015/02/ShareYourSalary-APIDemo.png"><img class="aligncenter size-full wp-image-915" src="{{ site.baseurl }}/assets/ShareYourSalary-APIDemo.png" alt="ShareYourSalary API Demo" width="987" height="731" /></a></p>
<p>If we tried to create a survey called 'demo' then all we need to do is execute the /survey/getByName/{name} API with that value. You can do this in Swagger, or since it's just a GET request, you can do it directly in your browser. Just browse to <a title="http://shareyoursalary-alexdglover.rhcloud.com/api/v1/survey/getByName/demo" href="http://shareyoursalary-alexdglover.rhcloud.com/api/v1/survey/getByName/demo" target="_blank">http://shareyoursalary-alexdglover.rhcloud.com/api/v1/survey/getByName/demo</a> and you should see the JSON response.</p>
<p>OK, at this point we should have a static home page and a working form. Next, we'll have to create dynamic pages for each survey that's created.</p>
<h3>Dynamic Pages</h3>
<p>For populating dynamic content, you can write your logic directly in F3's index.php, abstract it into individual classes, or you can do what I did and consolidate it all into one PageGenerator class. This will simplify our F3 routes (more on this later) and manage all of our dynamic views in one place.</p>
<p>For this project, we need 3 pages or Views (excluding the static home page):</p>
<ol>
<li>A page users hit after creating a survey. We'll call this 'thanks' as we'll be thanking users for creating a survey and enable them to invite others to participate</li>
<li>A page for users who've been invited to participate in an existing survey. We'll call this 'addResponse' because ultimately that's the only purpose for this page</li>
<li>A page for users (both invitees and the original survey creator) to review the results of the survey. We'll call this 'report'</li>
</ol>
<p>First, let's construct our controller that will get all of the relevant variables for these pages and then set the content. Let's review the code:</p>
<pre class="lang:php decode:true ">&lt;?php
class PageGenerator {
	// Instantiate variables
	var $surveyName;
	var $currency;
	var $period;
	var $minentries;
	private $db;

	// Construct the PageGenerator object with a Database object
	public function __construct(){
		$this-&gt;db = new Database();
	}
	
	// Utility function to convert survey names to a URL-friendly name
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


	// Function for populating the 'thanks' page (after a user creates a survey). This collects all of the relevant survey information into the 'survey' JSON object and sets the 'content' variable to the code in the thanks.htm file

	public function thanks($f3,$args){
		$surveys = $this-&gt;db-&gt;get_collection('surveys');
		// Convert name to URL friendly name
                $URLName = $this-&gt;convertNameToURLName($args['name']);
		$query = array('URLName' =&gt; $URLName);
                $cursor = $surveys-&gt;find($query);
                foreach ($cursor as $doc){
			$f3-&gt;set('survey',$doc);                	
                }
		$f3-&gt;set('content','thanks.htm');
		echo View::instance()-&gt;render('layout.htm');
	}


	// Function for populating the 'addResponse' page (the page invited users see). This collects all of the relevant survey information into the 'survey' JSON object and sets the 'content' variable to the code in the addResponse.htm file

	public function addResponse($f3,$args){
		$surveys = $this-&gt;db-&gt;get_collection('surveys');
		// Convert name to URL friendly name
                $URLName = $this-&gt;convertNameToURLName($args['name']);
                $query = array('URLName' =&gt; $URLName);
                $cursor = $surveys-&gt;find($query);
                foreach ($cursor as $doc){
			$f3-&gt;set('survey',$doc);                	
                }
		$f3-&gt;set('content','addResponse.htm');
		echo View::instance()-&gt;render('layout.htm');
	}


	// Function for populating the 'report' page. This collects all of the relevant survey information into the 'survey' JSON object and sets the 'content' variable to the code in the report.htm file

	public function report($f3,$args){
		$surveys = $this-&gt;db-&gt;get_collection('surveys');
		// Convert name to URL friendly name
                $URLName = $this-&gt;convertNameToURLName($args['name']);
                $query = array('URLName' =&gt; $URLName);		
                $cursor = $surveys-&gt;find($query);
		foreach ($cursor as $doc)
		{
                	if (count($doc['responses']) &lt; $doc['minEntries'])
			{
				$f3-&gt;set('minEntriesMet', false);
			}
			else
			{
				asort($doc['responses']);
				$f3-&gt;set('minEntriesMet', true);
				$averageSalary = round((array_sum($doc['responses']))/(count($doc['responses'])),2);
				$f3-&gt;set('averageSalary',$averageSalary);
				$percentDeltas = [];
				foreach($doc['responses'] as $response)
				{
					array_push($percentDeltas, (round((($response-$averageSalary)/$averageSalary),2) ) );
				}
				asort($percentDeltas);
				$f3-&gt;set('percentDeltas',$percentDeltas);
				// Median code borrowed from http://www.mdj.us/web-development/php-programming/calculating-the-median-average-values-of-an-array-with-php/			
				sort($doc['responses']);
				$count = count($doc['responses']); //total numbers in array
				$middleval = floor(($count-1)/2); // find the middle value, or the lowest middle value
				if($count % 2) { // odd number, middle is the median
					$median = $doc['responses'][$middleval];
				} else { // even number, calculate avg of 2 medians
					$low = $doc['responses'][$middleval];
					$high = $doc['responses'][$middleval+1];
					$median = (($low+$high)/2);
				}
				$f3-&gt;set('median',$median);
			} //End of minEntriesMet==true block
			$f3-&gt;set('survey',$doc);			
		}
		$f3-&gt;set('content','report.htm');
		echo View::instance()-&gt;render('layout.htm');
	}
}
?&gt;</pre>
<p>As you can see, the functions follow a very similar pattern - we get a survey name input (via F3's $args variable), grab the relevant survey data from the DB, set one (or more) JSON objects to be consumed in the page, and set the 'content' variable to the appropriate page. From inside of the View, we'll grab the individual elements out of the array and use it in the display or JavaScript.</p>
<p>Now for the Views. Let's walk through the app as a user would, just for intuitiveness. Our user has created a survey successfully, so let's thank them for creating it and show them the details of the survey so they can confirm everything is right. Let's also add a mechanism for the user to invite their friends and colleagues through social media. Our survey creator might also be participating, so let's enable them to add a response right away as well. Finally, we need to provide a link to the survey report where users can view the results. Remember, our PageGenerator controller is going to pass us all of the data we need in one array object titled $survey. Let's take a look at the HTML:</p>
<pre class="lang:default decode:true" title="thanks.htm">&lt;h2&gt;Thanks for creating a survey!&lt;/h1&gt;
&lt;div class="row"&gt;
        &lt;div class="span5"&gt;
		&lt;legend&gt;Survey Details&lt;/legend&gt;
		&lt;p&gt;&lt;strong&gt;Survey name&lt;/strong&gt;&lt;br /&gt;&lt;?php echo $survey['name']; ?&gt;&lt;/p&gt;
		&lt;p&gt;&lt;strong&gt;Salary currency&lt;/strong&gt;&lt;br /&gt;&lt;?php echo $survey['currency']; ?&gt;&lt;/p&gt;
		&lt;p&gt;&lt;strong&gt;Salary period&lt;/strong&gt;&lt;br /&gt;&lt;?php echo $survey['period']; ?&gt;&lt;/p&gt;
		&lt;p&gt;&lt;strong&gt;Minimum number of responses before results published&lt;/strong&gt;&lt;br /&gt;&lt;?php echo $survey['minEntries']; ?&gt;&lt;/p&gt;
		&lt;p&gt;&lt;strong&gt;Current number of responses&lt;/strong&gt;&lt;br /&gt;&lt;?php echo count($survey['responses']); ?&gt;&lt;/p&gt;
	&lt;/div&gt;
	&lt;div class="span5"&gt;
		&lt;legend&gt;Next Steps&lt;/legend&gt;
		&lt;ol&gt;
			&lt;li&gt;Invite others to participate by sharing&lt;br /&gt;
			&lt;a target="_blank" href="&lt;?php echo "http://www.facebook.com/sharer/sharer.php?u=" . "http://$_SERVER[HTTP_HOST]" . "/addResponse/" . $survey['URLName']; ?&gt;"&gt;&lt;img src="/ui/img/facebook_001.jpg" style="width:32px; height:32px;" /&gt;&lt;/a&gt;
			&lt;a target="_blank" href="&lt;?php echo "https://twitter.com/intent/tweet?text=How%20much%20do%20you%20make%3F&amp;url=" . "http://$_SERVER[HTTP_HOST]" . "/addResponse/" . $survey['URLName']; ?&gt;"&gt;&lt;img src="/ui/img/Twitter_001.jpg" style="width:32px; height:32px;" /&gt;&lt;/a&gt;
			&lt;a target="_blank" href="&lt;?php echo "http://www.linkedin.com/shareArticle?mini=true&amp;url=" . "http://$_SERVER[HTTP_HOST]" . "/addResponse/" . $survey['URLName'] . "&amp;title=Salary%20Survey"; ?&gt;"&gt;&lt;img src="/ui/img/Linkedin_001.jpg" style="width:32px; height:32px;" /&gt;&lt;/a&gt;
			&lt;a href="&lt;?php echo "http://pinterest.com/pin/create/button/?url=" . "http://$_SERVER[HTTP_HOST]" . "/addResponse/" . $survey['URLName']; ?&gt;"  class="pin-it-button" count-layout="horizontal"&gt;&lt;img src="/ui/img/Pinterest_001.jpg" style="width:32px; height:32px;" /&gt;&lt;/a&gt;
&lt;br /&gt;
			Or by sending them this link:&lt;br /&gt;&lt;a href="&lt;?php echo "http://$_SERVER[HTTP_HOST]" . "/addResponse/" . $survey['URLName']; ?&gt;"&gt;&lt;?php echo "http://$_SERVER[HTTP_HOST]" . "/addResponse/" . $survey['URLName']; ?&gt;&lt;/a&gt;&lt;/li&gt;
			&lt;li&gt;Enter your salary&lt;br /&gt;
				&lt;form&gt;
				&lt;div class="input-append"&gt;
					&lt;input name="response" type="text" pattern="[1-9]{1}[0-9]*\.?[0-9]{0,2}" placeholder="Enter your salary" /&gt;
					&lt;span class="add-on"&gt;&lt;?php echo $survey['currency'] ?&gt;&amp;nbsp;&lt;?php echo $survey['period'] ?&gt;&lt;/span&gt;
				&lt;/div&gt;
				&lt;input id="submit" type="submit" class="btn btn-success" value="Submit" /&gt;
				&lt;/form&gt;
			&lt;/li&gt;
			&lt;li&gt;
				Once the minimum number of entries are met, see the salary analysis report here: &lt;a href="&lt;?php echo "http://$_SERVER[HTTP_HOST]" . "/report/" . $survey['URLName']; ?&gt;"&gt;&lt;?php echo "http://$_SERVER[HTTP_HOST]" . "/report/" . $survey['URLName']; ?&gt;&lt;/a&gt;
			&lt;/li&gt;
		&lt;/ol&gt;
	&lt;/div&gt;
&lt;/div&gt;
&lt;script&gt;
	$( "form" ).on( "submit", function( event ) {
		event.preventDefault();
		var datastring = $( "form" ).serialize();
		$.ajax({
		    type: "POST",
		    url: "/api/v1/survey/addResponse/&lt;?php echo $survey['URLName'] ?&gt;",
		    data: datastring,
		    dataType: "json",
		    success: function(data) {
			alert("Thanks! You've successfully entered your salary information.");
		    },
		    error: function(){
		          alert('Oops... something wrong');
		    }
		});		
	});
&lt;/script&gt;</pre>
<p>One point of interest - you may have noticed the "pattern" property set on the one ofthe HTML inputs. This is a regular expression that tells the input field what valuesare allowed. Let's examine this pattern by breaking it up:<br />
pattern="[1-9]{1}[0-9]*\.?[0-9]{0,2}"</p>
<p>[1-9]{1} - this means we want one and only one character, and it has to be a digit 1-9. This prevents users from submitting a value that starts with a leading zero.</p>
<p>[0-9]* - this means any character 0-9, an unlimited number of times.</p>
<p>\.? - this means we're expecting zero or one period. This allows our users to enter a decimal point if they want, but at most one.</p>
<p>[0-9]{0,2} - this means we'll accept zero, one, or two of any characters between 0 and 9.</p>
<p>What this all boils down to is a user can enter any number with no decimal places, a single decimal place, or two decimal places, and nothing else. No symbols, letters, or illegitimate formats. This ensures we have clean valid numbers to do mathematical operations on later for the 'report' page.</p>
<p>The 'addResponse' page is simpler and re-uses a lot of the same code as the 'thanks' page, so let's jut jump right into it.</p>
<pre class="lang:default decode:true" title="addResponse.htm">&lt;h2&gt;You've been invited!&lt;/h1&gt;
&lt;div class="row"&gt;
        &lt;div class="span5"&gt;
		&lt;legend&gt;Wait, what's going on?&lt;/legend&gt;
		&lt;p&gt;You've been invited to participate in an anonymous survey about salaries. Someone has already created the survey and invited you and others to participate. Your salary information is totally anonymous - no login or email address is required. To ensure anonymity, the results of the survey aren't published until a minimum number of responses are made (see below).&lt;/p&gt; 

		&lt;p&gt;&lt;strong&gt;Survey name&lt;/strong&gt;&lt;br /&gt;&lt;?php echo $survey['name']; ?&gt;&lt;/p&gt;
		&lt;p&gt;&lt;strong&gt;Minimum number of responses before results published&lt;/strong&gt;&lt;br /&gt;&lt;?php echo $survey['minEntries']; ?&gt;&lt;/p&gt;
		&lt;p&gt;&lt;strong&gt;Current number of responses&lt;/strong&gt;&lt;br /&gt;&lt;?php echo count($survey['responses']); ?&gt;
	&lt;/div&gt;
	&lt;div class="span5"&gt;
		&lt;legend&gt;Next Steps&lt;/legend&gt;
		&lt;ol&gt;
			&lt;li&gt;Enter your salary&lt;br /&gt;
				&lt;form&gt;
				&lt;div class="input-append"&gt;
					&lt;input name="response" type="text" pattern="[1-9]{1}[0-9]*\.?[0-9]{0,2}" placeholder="Enter your salary" /&gt;
					&lt;span class="add-on"&gt;&lt;?php echo $survey['currency'] ?&gt;&amp;nbsp;&lt;?php echo $survey['period'] ?&gt;&lt;/span&gt;
				&lt;/div&gt;
				&lt;p&gt;&lt;input id="submit" type="submit" class="btn btn-success" value="Submit" /&gt;&lt;/p&gt;
				&lt;/form&gt;
			&lt;/li&gt;
			&lt;li&gt;
				Once the minimum number of entries are met, see the salary analysis report here: &lt;a href="&lt;?php echo "http://$_SERVER[HTTP_HOST]" . "/report/" . $survey['URLName']; ?&gt;"&gt;&lt;?php echo "http://$_SERVER[HTTP_HOST]" . "/report/" . $survey['URLName']; ?&gt;&lt;/a&gt;
			&lt;/li&gt;
		&lt;/ol&gt;
	&lt;/div&gt;
&lt;/div&gt;
&lt;script&gt;
	$( "form" ).on( "submit", function( event ) {
		event.preventDefault();
		var datastring = $( "form" ).serialize();
		$.ajax({
		    type: "POST",
		    url: "/api/v1/survey/addResponse/&lt;?php echo $survey['URLName'] ?&gt;",
		    data: datastring,
		    dataType: "json",
		    success: function(data) {
			alert("Thanks! You've successfully entered your salary information.");
		    },
		    error: function(){
		          alert('Oops... something wrong');
		    }
		});		


	});
&lt;/script&gt;</pre>
<p>Easy right? OK, the last page is the 'report' page where we'll give some basic statistics from the survey results and a nice little chart. The statistics are pretty easy to calculate, and for the chart we'll use Google Charts API via JavaScript. Let's take a look.</p>
<pre class="lang:default decode:true " title="report.htm">&lt;h2&gt;Salary Analysis for Survey '&lt;?php echo $survey['name']; ?&gt;'&lt;/h1&gt;
&lt;div class="row"&gt;
	&lt;?php if($minEntriesMet) { ?&gt;
        &lt;div class="span5"&gt;
		&lt;legend&gt;Survey Details&lt;/legend&gt;
		&lt;p&gt;&lt;strong&gt;Survey name&lt;/strong&gt;&lt;br /&gt;&lt;?php echo $survey['name']; ?&gt;&lt;/p&gt;
		&lt;p&gt;&lt;strong&gt;Average salary&lt;/strong&gt;&lt;br /&gt;&lt;?php echo $averageSalary ?&gt; &lt;?php echo $survey['currency']; ?&gt; &lt;?php echo $survey['period']; ?&gt;&lt;/p&gt;
		&lt;p&gt;&lt;strong&gt;Median salary&lt;/strong&gt;&lt;br /&gt;&lt;?php echo $median ?&gt; &lt;?php echo $survey['currency']; ?&gt; &lt;?php echo $survey['period']; ?&gt;&lt;/p&gt;
		&lt;!--&lt;p&gt;&lt;strong&gt;Salary currency&lt;/strong&gt;&lt;br /&gt;&lt;?php echo $survey['currency']; ?&gt;&lt;/p&gt;
		&lt;p&gt;&lt;strong&gt;Salary period&lt;/strong&gt;&lt;br /&gt;&lt;?php echo $survey['period']; ?&gt;&lt;/p&gt;--&gt;
		&lt;p&gt;&lt;strong&gt;Minimum number of responses before results published&lt;/strong&gt;&lt;br /&gt;&lt;?php echo $survey['minEntries']; ?&gt;&lt;/p&gt;
		&lt;p&gt;&lt;strong&gt;Current number of responses&lt;/strong&gt;&lt;br /&gt;&lt;?php echo count($survey['responses']); ?&gt;
	&lt;/div&gt;
	&lt;div class="span5"&gt;
		&lt;legend&gt;Relative Salary Comparison&lt;/legend&gt;
		&lt;div id="chart_div" style="width: 100%; height: 300px;"&gt;&lt;/div&gt;
	&lt;/div&gt;
	&lt;?php } else { ?&gt;
	&lt;div class="span12"&gt;
		Uh oh, minimum number of responses haven't been met yet. Minimum number of responses is &lt;?php echo $survey['minEntries']; ?&gt; and there have been &lt;?php echo count($survey['responses']); ?&gt; responses so far.&lt;br /&gt;&lt;br /&gt;No report data available.
	&lt;/div&gt;
	&lt;?php } ?&gt;
&lt;/div&gt;
&lt;script type="text/javascript" src="https://www.google.com/jsapi?autoload={'modules':[{'name':'visualization','version':'1','packages':['corechart']}]}"&gt;&lt;/script&gt;
&lt;!--&lt;div id="chart_div" style="width: 100%; height: 500px;"&gt;&lt;/div&gt;--&gt;
&lt;script&gt;
      google.setOnLoadCallback(drawChart);
      function drawChart() {
	var dataTable =	new google.visualization.DataTable();
	dataTable.addColumn('number', 'Salary');
	dataTable.addColumn('number', '% Delta');
	&lt;?php for($i=0; $i &lt; count($survey['responses']); $i++){
		echo 'dataTable.addRow(['. $survey['responses'][$i]. ',' . $percentDeltas[$i] . ']);';
	};?&gt;

	var formatter = new google.visualization.NumberFormat({
	    fractionDigits: 2,
	    prefix: '$'
	});

	formatter.format(dataTable, 0); // Apply formatter to salary column.

	var formatter = new google.visualization.NumberFormat({
	    fractionDigits: 2,
	    pattern: '###%'
	});

	formatter.format(dataTable, 1); // Apply formatter to percent delta column.
	
        var options = {
          title: 'Salary comparison',
          hAxis: {title: 'Salary', minValue: 0, maxValue: 10, format: '$#'},
          vAxis: {title: 'Percent above/below average', minValue: -1, maxValue: 1, format: '#%'},
          legend: 'none'
        };

        var chart = new google.visualization.ScatterChart(document.getElementById('chart_div'));

        chart.draw(dataTable, options);
      }
&lt;/script&gt;</pre>
<p>OK, so theoretically our PageController and our four Views should all be working at this point, we just need to add routes to our index.php for these pages. Much like our API routes, we'll specify a URL that includes variables in the URL string itself. Let's create a route for each of our three new Views:</p>
<pre class="lang:php decode:true" title="index.php">// Page generator - class of functions for populating pages
$f3-&gt;route('GET /thanks/@name','PageGenerator-&gt;thanks');
$f3-&gt;route('GET /report/@name','PageGenerator-&gt;report');
$f3-&gt;route('GET /addResponse/@name','PageGenerator-&gt;addResponse');</pre>
<p>Since these are normal web pages, we'll use the default GET method. Next we specify the path including the variable prefixed with the @ sign, and finally we tell F3 which class and function to map the route to (in the format of ClassName-&gt;functionName). Now we can hit those URLs with 'demo' as the name argument and we should see the pages populated with the details of the 'demo' survey object.</p>
<p>That's it - we now have a full working application including an API and a very decent user interface (it's even mobile friendly, thanks to Bootstrap).</p>
<h3>Deployment Notes and 'Cloudiness'</h3>
<p>This is already an insanely long write-up for this project, but I want to push through to make a couple important comments.</p>
<p>Before your F3 app is live in production, we need to make one more small change. By default, F3 will log error output directly to the browser. This is great for debugging, but unfortunately it can spit out sensitive information, like the username and password for our MongoDB. To disable this level of error logging, simply modify the 'DEBUG' F3 variable to 0, like this:</p>
<pre class="lang:php decode:true ">$f3-&gt;set('DEBUG',0);</pre>
<p>Simple.</p>
<p>Deployment with OpenShift is trivial, as you're really doing continuous delivery - every time you push your code, OpenShift redeploys your environment with the new code. If you haven't been doing this along the way, you just need 3 commands to deploy your code:</p>
<pre class="lang:sh decode:true"># Execute this from within your OpenShift project directory
git add -A # add all changes to the git index
git commit -m 'add your commit message'
git push</pre>
<p>Done. You can now hit the app by browsing to your OpenShift-generated DNS name. Adding your own custom DNS name is easy, and I'll cover that in a separate post.</p>
<p>As for cloudiness - the application might not be that complex and my object-oriented skills are garbage, but from an infrastructure/hosting perspective, it's the Holy Grail. Here's why:</p>
<ul>
<li>The app doesn't care about DNS names. You can hit the app at <a href="http://shareyoursalary.alexdglover.com" target="_blank">http://shareyoursalary.alexdglover.com</a> or <a href="http://shareyoursalary-alexdglover.rhcloud.com" target="_blank">http://shareyoursalary-alexdglover.rhcloud.com</a>, or even <a href="http://www.shareyoursalary.gq/" target="_blank">http://www.shareyoursalary.gq/</a> and the app works perfectly. You can deploy it anywhere, change DNS names whenever, you can give customers/users their own custom 'vanity' URLs, and the app will give consistent results. It's extremely flexible and portable.</li>
<li>There are no sessions to worry about, making load-balancing extremely easy.</li>
<li>Since all of the app functionality is API based, we can scale the application horizontally just by adding more PHP instances behind the load balancer.</li>
<li>By using a NoSQL database (MongoDB), the database layer can also be scaled horizontally.</li>
<li>All of the code is deployment agnostic - since we're using environment variables for the database connection details, this same source can be deployed to development, QA, and production environments without any changes.</li>
<li>There are no high-cost or proprietary application components. You can run this app in any cloud hosting environment, on a number of operating systems, using off-the-shelf images/services in many cases.</li>
<li>The deployment is as easy as putting the contents of our project directory in the document root of the Apache/PHP servers. There's no configuration necessary.There is no DB schema to set up as part of the app deployment - the application will create the collection (read: table) on the fly and start inserting records.</li>
</ul>
<p>In short, it should be very easy for anybody with minimal cloud automation training to deploy this application in a highly available and scalable model. Specifically, the app can handle instance failures in any tier and can scale horizontally to support hundreds of thousands of concurrent users without human intervention or configuration. With all of the advancements in cloud and automation, this should be the de facto standard for all but the largest and most complex apps.</p>
<p>As always, I hope this post has been helpful and let me know if you have any questions.</p>
