---
title: Web Scraping, PHP, and Wheel of Fortune
date: 2013-04-16 21:13:22.000000000 -05:00
categories:
- Fun IT Projects
tags:
- cron
- php
- scraping
- web scraping
---
**Be advised** This post is quite old ({{ page.date | date_to_string }}) and any code may be out of date. Proceed with caution.
{: .notice--warning}

<p>My fiancee loves Wheel of Fortune and watches whenever she can. The clever folks over at Sony (producers of Wheel of Fortune) introduced a loyalty program called "Wheel Watchers." People who sign up get a "Spin ID," and if your Spin ID is chosen for a given episode, you win one of the prizes they gave away on the show. Only catch is you have to watch every night.</p>
<p>This is a lot of random background information, but there's a reason. My fiancee asked me to write an application that would check the Spin ID every day and notify us if we won. This seemed like a great reason to learn some <a href="http://en.wikipedia.org/wiki/Web_scraping" target="_blank">web scraping</a> with PHP (although you could probably do this in just about any language).</p>
<h3>Pulling Raw Data</h3>
<p>First things first, you need a decent website to scrape the information from! Strangely, the official Wheel of Fortune site doesn't offer up the winning spin IDs. Luckily, there are a handful of websites that do report the winning numbers. For my application, I'll be using <a href="http://wheeloffortuneclub.blogspot.com/">http://wheeloffortuneclub.blogspot.com</a></p>
<p>We'll pull the entire web page first and then parse for the Spin ID later. In PHP, this is quite simple:</p>

```php
<?php
$data = file_get_contents('http://wheeloffortuneclub.blogspot.com/');
?>
```

<p>Keep in mind, many site admins will not take kindly to you scraping their site especially if you're doing it frequently. In this example, we'll only need to scrape the information once a day, so it shouldn't be a problem.</p>
<h3>Parsing for the Spin ID</h3>
<p>When scraping web pages, regular expressions come in handy. To pull the specific data you're looking for, you may need to use a clever combination of identifying content as well as identifying HTML tags and attributes to retrieve the data. In the case of the Spin ID, it's two capital letters and 6-7 numbers. This is a pretty specific format, so it'll be pretty easy to pull using regex.</p>
<p>Now, regex syntax can be tough if you don't use it on a regular basis. <a href="http://regex101.com/" target="_blank">Regex 101</a> is an awesome site to use as a reference for regex syntax or to test your expressions.</p>
<p>For the Spin ID, our regular expression is "[A-Z][A-Z]\d{6,}". This translates to two capital letters ([A-Z][A-Z]) followed by 6 or more numbers (\d{6,}). We'll create a variable for our regular expression and parse our previously fetched web page to look for the expression using preg_match, which will return the first match:</p>

```php
$regex = '/[A-Z][A-Z]\d{6,}/';
preg_match($regex,$data,$match);
```

<p>We're passing three parameters to preg_match - the pattern we're seeking, the string subject, and an output variable ($match in this case).</p>
<p>The $match variable is actually an array, and so we'll refer to the first object in the array to get the string. For now, we'll just echo the variable out to the page to confirm that everything's working!</p>
```php
$regex = '/[A-Z][A-Z]\d{6,}/';
preg_match($regex,$data,$match);
echo $match[0];
```

<p>So the complete code looks like this:</p>

```php
<?php
$data = file_get_contents('http://wheeloffortuneclub.blogspot.com/');
$regex = '/[A-Z][A-Z]\d{6,}/';
preg_match($regex,$data,$match);
echo $match[0];
?>
```

<h3>Automation with Cron and Email</h3>
<p>So we've got a PHP page that will parse and return the most recent winning Spin ID. So what? I could have just browsed to the Spin ID website and gotten the same information. We need to automate the parsing and compare it to our specific Spin ID (to see if we're a winner) and contact us if it's a match. If it's a match, we'll send an email to notify us. Here's the code in it's entirety:</p>
```php
<?php
$data = file_get_contents('http://wheeloffortuneclub.blogspot.com/');
$regex = '/[A-Z][A-Z]\d{6,}/';
preg_match($regex,$data,$match);
echo $match[0];
echo "<br />";
if ($match[0]=="KW6426861"){
  echo "Match!!!";
  mail("alexdglover@gmail.com","Spin ID Match!!!","Spin ID Match!!!");
}
else{
  echo "Not a match!!!";
}
?>
```
<p>The Spin ID "KW6426861" was the most recent winning ID at the time I wrote this script, and so the check resolved to true and sent me a convenient notification email. Awesome. Now, to finish our project, we just need to regularly execute the PHP script with a Cron job. If you're using your home server to host, you can just write a crontab entry using php -f /path/to/your/php/script.php and execute it at whatever interval you want.</p>
<p>If you are using hosting externally, most CPanels will offer a cron functionality. Again, you just need to provide the command ("php -f" in this case), the path to your php script, and then your interval. I used " 0 */12 * * * " to check every 12 hours.</p>
<p>That's it! A very simple but powerful PHP script in just 14 lines of code!</p>
<p><strong>Feb 1, 2015 Update:</strong> For those who don't have their own servers or can't be bothered to build their own SpinID monitoring service, check out <a href="https://wheelnotify.com/#!/" target="_blank">WheelNotify.com</a> - for just $1/month, the service notify you via email, text, and/or phone if your SpinID is ever a winner on Wheel Of Fortune. Awesome!</p>
