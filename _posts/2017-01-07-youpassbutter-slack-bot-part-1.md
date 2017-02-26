---
title: YouPassButter Slack Bot, Part 1
date: 2017-01-07 11:34:38.000000000 -06:00
categories:
- IT/Software Projects
- How-to Guides
tags:
- ruby
- sinatra
- slack
- slash commands
header:
  teaser: /assets/anim.gif
  overlay_image: /assets/rick-look.jpg
  overlay_color: "#5e616c"
  overlay_filter: rgba(0, 0, 0, 0.5)
  cta_label: "<i class='fa fa-github'></i> GitHub Repo"
  cta_url: "https://github.com/alexdglover/you-pass-butter-bot"
  caption: Image courtesy of Adult Swim, YouTube
excerpt: A dirt simple Slack bot primer, using Ruby, Sinatra, Heroku, and Slash commands
---
<h2>TL;DR Section</h2>
<p>Just looking for the app? Just click the "Add to Slack" button to install the YouPassButter bot</p>
<p style="text-align: center;">
  <a href="https://slack.com/oauth/authorize?scope=commands&client_id=122992570306.122925378483">
    <img src="{{ site.baseurl }}/assets/add_to_slack.png" srcset="https://platform.slack-edge.com/img/add_to_slack.png 1x, https://platform.slack-edge.com/img/add_to_slack@2x.png 2x" alt="Add to Slack" width="139" height="40" />
  </a>
</p>

<p>Don't care about the context, just looking for a general guide to building a Slack bot with Ruby and Sinatra? Skip down to the "Implementation" section.</p>

<p>Prefer to just go straight to the source? <a href="https://github.com/alexdglover/you-pass-butter-bot">Check out the code on GitHub</a>.</p>

<p style="text-align: center;">
  <a href="https://github.com/alexdglover/you-pass-butter-bot">
    <img class="aligncenter size-medium wp-image-1050" src="{{ site.baseurl }}/assets/GitHub_Logo-300x123.png" alt="GitHub_Logo" width="300" height="123" />
  </a>
</p>

<h2>Background/Context</h2>
<p>I've wanted to build a Slack bot for a while now, but didn't have any ideas for what the bot should do. Then it hit me - a Rick and Morty bot for getting funny quotes and memes posted to Slack.  And what robot better represents Rick and Morty then the butter-passing robot Rick built</p>
<p><iframe width="560" height="315" src="https://www.youtube.com/embed/ekP0LQEsUh0?start=32" frameborder="0" allowfullscreen="allowfullscreen"></iframe></p>
<p>I'm also starting a new job in a few days, and so I was looking for an excuse to write something in Ruby.</p>
<p>Finally, like all of my projects, this took a couple of iterations before I was really happy with it, so this post will come in a few parts.</p>
<h2>Requirements</h2>
<ul>
<li>As a user, I want to be able to summon a pre-defined Rick and Morty image or gif</li>
<li>As a user, I want to be able to summon a dynamically generated Rick and Morty meme with Slash Command inputs being used to generate meme</li>
</ul>
<h2>Mechanics</h2>
<p>Slack bots (and chat bots in general) <strong>can</strong> be very complex, but they don't have to be. With Slack, the simplest implementation IMO is the "Slash Command" bots. These aren't really "bots" per se, but rather a few pre-defined Slash Commands that call out to a web service endpoint and get a response. When someone installs this "bot" it just makes the Slash Commands available to their team.</p>
<p>Behind the scenes, these Slash Commands are associated with a URL endpoint, e.g. https://my.web.service.com/route. When you execute a Slash Command, Slack makes a GET or a POST to that URL endpoint including a payload that includes, among other things, any arguments that were passed to the slack command.</p>
<p>For example, let's assume our Slack app has "/foo" defined as a Slash Command that is associated with https://my.web.service.com/foo. If a user executes "/foo bar" from the "general" channel, Slack will make a POST request to https://my.web.service.com/foo with the following payload:</p>

```
token=gIkuvaNzQIHg97ATvDxqgjtO
team_id=T0001
team_domain=example
channel_id=C2147483705
channel_name=general
user_id=U2147483697
user_name=Alex
command=/foo
text=bar
response_url=https://hooks.slack.com/commands/1234/5678
```
<p>Now, if you just want to make an HTTP request and not get any result back (i.e. kicking off a job), you could simply define the Slash Command and be done with it (assuming the receiving endpoint was already set up).</p>
<p>For all other use cases, your endpoint needs to either</p>
<ul>
<li>return relevant data in the response body within 3 seconds of the initial request OR</li>
<li>make a delayed response by POSTing to the response_url in the initial request</li>
</ul>
<p>For our YouPassButter bot, we'll use both of these approaches. We can use the immediate response approach for any existing images or GIFs. For the generated memes, we need to use the delayed response approach because of the latency of generating a new meme each time.</p>
<h2>Implementation</h2>
<p>This is a fairly easy project - we just need to deploy a web service that can take POST and some arguments, and either respond immediately or make a POST request to a Slack endpoint. As I mentioned, I wanted to brush up on my Ruby so I decided to use Sinatra for this project.</p>
<p>I have built Slack bots previously, but never in Ruby. To get myself grounded, I took a look at this tutorial to get started: https://www.sitepoint.com/building-a-slackbot-with-ruby-and-sinatra/ (Plus I like to give credit to the other blogs/sites that inspire and teach me).</p>
<p>I started by writing a very simple Sinatra app, just to smoke test that everything was working.</p>
<p>Here's the directory structure of the application:</p>

```shell
rick-and-morty-bot/
|--app.rb
|--config.ru
|--Gemfile
```

<p>To start, the Gemfile just contained the 'sinatra' and 'json' gems:</p>

```ruby
source 'https://rubygems.org'

gem 'sinatra'
gem 'json'
```

<p>The config.ru is very barebones, just enough to deploy the app to Heroku:</p>

```ruby
require './app'
run Sinatra::Application
```

<p>And finally, our initial app.rb:</p>

```ruby
require 'sinatra'
require 'json'

set :protection, :except => [:json_csrf] # This is a Sinatra setting I needed to address 40x errors

################################################
# Generic routes
################################################

get '/' do
  string_as_json_response "hello world"
end


################################################
# Image related routes
################################################

post '/you-pass-butter' do
  image_responpse "https://media.giphy.com/media/Fsn4WJcqwlbtS/giphy.gif"
end


################################################
# Utility functions
# These functions handle generic formatting of the response body
# to return to Slack
################################################

def image_response url
  message = {
    :response_type => "in_channel",
    :attachments => [
      { :image_url => url }
    ]
  }
  hash_as_json_response message
end

def hash_as_json_response message
  content_type :json
  message.to_json
end

def string_as_json_response message
  content_type :json
  { :text => message }.to_json
end
```

<p>That's it, super simple. If our Sinatra app receives a POST request at '/you-pass-butter' then our web service will return a JSON response that includes the GIF attachment:</p>
```javascript
{
  "response_type": "in_channel",
  "attachments": [
    { "image_url": "https://media.giphy.com/media/Fsn4WJcqwlbtS/giphy.gif" }
  ]
}
```

<p>The "response_type": "in_channel" tells Slack that this message should be shown to everyone in the channel, not just the user who executed the Slack command. On that note, this is probably a good time to link to some of Slack's documentation and resources:</p>
<p><a href="https://api.slack.com/slash-commands">Slash Command Bot Overview</a></p>
<p><a href="https://api.slack.com/docs/message-attachments">Attaching content and links to messages</a></p>
<p><a href="https://api.slack.com/docs/message-formatting">Basic Message Formatting</a></p>
<p><a href="https://api.slack.com/docs/messages/builder?msg=%7B%22text%22%3A%22Thanks%20for%20visiting%20AlexDGlover.com%22%7D">Message Formatting "Playground"</a> (useful for verifying the syntax/structure of your JSON response)</p>
<p>At this point, we can deploy our app to Heroku and smoke test it. By cURL'ing that endpoint, we can verify that the app is working correctly. Adding new routes is super simple, we just follow the same pattern for our "/you-pass-butter" route and provide a different image URL to the image_response method. Let's add a few more routes:</p>

```ruby
require 'sinatra'
require 'json'

set :protection, :except => [:json_csrf] # This is a Sinatra setting I needed to address 40x errors

################################################
# Generic routes
################################################

get '/' do
  string_as_json_response "hello world"
end


################################################
# Image related routes
################################################

post '/you-pass-butter' do
  image_responpse "https://media.giphy.com/media/Fsn4WJcqwlbtS/giphy.gif"
end

post '/wriggety-wriggety-wrecked-son' do
  image_response "http://www.reactiongifs.us/wp-content/uploads/2016/02/riggity_wrecked_son_rick_morty.gif"
end

post '/planning-for-failure' do
  image_response "http://2.media.dorkly.cvcdn.com/78/27/5e7374300a84037f2bdbc061f2e69211.jpg"
end

post '/where-are-my-testicles-summer' do
  image_response "http://i.imgur.com/xOcpvUM.png"
end

post '/the-answer-is-dont-think-about-it' do
  image_response "http://i.imgur.com/L88mBul.jpg"
end

post '/its-getting-weird' do
  image_response "https://i.imgur.com/bBLnvGu.jpg"
end

post '/i-just-wanna-die' do
  image_response "http://i.imgur.com/CkmvRq8.jpg"
end

post '/wubalubadubdub' do
  image_response "http://i.imgur.com/fub72FV.png"
end


################################################
# Utility functions
# These functions handle generic formatting of the response body
# to return to Slack
################################################

def image_response url
  message = {
    :response_type => "in_channel",
    :attachments => [
      { :image_url => url }
    ]
  }
  hash_as_json_response message
end

def hash_as_json_response message
  content_type :json
  message.to_json
end

def string_as_json_response message
  content_type :json
  { :text => message }.to_json
end
```

<p>Now that we have our web service up and running, we just need to create the integration on the Slack side. If you're just creating Slash Commands for your team, this is super easy and you don't have to create a true Slack "app." Just log on to your Slack team, click on the dropdown next to your name, choose "Apps & integrations," then search for "Slash Commands" (assuming you don't already have this app installed).</p>

<p>
  <a href="{{ "/assets/anim.gif" | absolute_url }}">
    <img class="aligncenter size-full wp-image-1038" src="{{ site.baseurl }}/assets/anim.gif" alt="" width="1023" height="663" />
  </a>
</p>

<p>Once you pull up the Slash Commands app, click on "Add Configuration." From there, you can provide whatever keyword you want for your Slash Command, then click "Add Slash Command Integration." On the next screen, provide the command name you want to use, and hit "Add Slash Command Integration."</p>
<p><a href="{{ "/assets/anim-1.gif" | absolute_url }}"><img class="aligncenter size-full wp-image-1039" src="{{ site.baseurl }}/assets/anim-1.gif" alt="" width="1023" height="663" /></a></p>
<p>On the next screen, there is a bunch of useful information about Slash Commands at the top, followed by configuration elements for our Slash Command. Let's change our command name to /you-pass-butter, and we'll set the URL to our application endpoint. In my case, that endpoint is https://rick-and-morty-bot.herokuapp.com/you-pass-butter. Also note that you can use GET or POST, <strong>but this only applies to custom Slash Commands you set up as part of your Slack 'team.'</strong> If you want to publish your Slash Command as an app, you need to use POST. In our case, we don't care which team the request is originating from, so we can ignore the token. You can also provide help text along with your slack command if you so choose.</p>
<p><a href="{{ "/assets/anim-2.gif" | absolute_url }}"><img class="aligncenter size-full wp-image-1040" src="{{ site.baseurl }}/assets/anim-2.gif" alt="" width="1007" height="1005" /></a></p>
<p>That's it! Now you should be able to go to your Slack channel and execute '/you-pass-butter' and get the image returned!</p>
<p><a href="{{ "/assets/anim-3.gif" | absolute_url }}"><img class="aligncenter size-full wp-image-1042" src="{{ site.baseurl }}/assets/anim-3.gif" alt="Testing Slash Command" width="1007" height="762" /></a></p>
<p>Awesome!</p>
<h2><a href="{{ "/youpassbutter-slack-bot-part-2/" | absolute_url }}">Part 2</a></h2>
<p>In <a href="{{ "/youpassbutter-slack-bot-part-2/" | absolute_url }}">Part 2</a>, we'll cover</p>
<ul>
<li>handling our second requirement, namely generating dynamic memes and POSTing them back to Slack</li>
<li>packaging our Slash Commands as a Slack app, including the OAuth configuration needed for the "Add to Slack" button</li>
<li>Re-structuring our Slash Commands and routes to get more functionality</li>
</ul>
<p>Stay tuned!</p>
