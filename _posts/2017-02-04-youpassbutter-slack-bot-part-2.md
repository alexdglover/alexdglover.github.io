---

title: YouPassButter Slack Bot, Part 2
date: 2017-02-04 10:00:23.000000000 -06:00



categories:
- Fun IT Projects
- How-to Guides
tags:
- ruby
- sinatra
- slack
- slash commands
meta:
  _edit_last: '1'
  _s2mail: 'yes'
  _publicize_twitter_user: "@alexdglover"
  _thumbnail_id: '1070'
  _wpas_done_all: '1'
  _jetpack_dont_email_post_to_subs: '1'
  _wpas_skip_16584087: '1'
  _wpas_skip_1477652: '1'
  _wpas_skip_1477650: '1'
---
<h2>TL;DR Section</h2>
<p>Just looking for the app? Just click the "Add to Slack" button to install the YouPassButter bot</p>
<p><a href="https://slack.com/oauth/authorize?scope=commands&amp;client_id=122992570306.122925378483"><img src="{{ site.baseurl }}/assets/add_to_slack.png" srcset="https://platform.slack-edge.com/img/add_to_slack.png 1x, https://platform.slack-edge.com/img/add_to_slack@2x.png 2x" alt="Add to Slack" width="139" height="40" /></a></p>
<p>Prefer to just go straight to the source? <a href="https://github.com/alexdglover/you-pass-butter-bot">Check out the code on GitHub</a>.</p>
<h2><a href="https://github.com/alexdglover/you-pass-butter-bot"><img class="aligncenter size-medium wp-image-1050" src="{{ site.baseurl }}/assets/GitHub_Logo-300x123.png" alt="GitHub_Logo" width="300" height="123" /></a>Where we left off</h2>
<p>If you haven't ready it already, check out <a href="http://alexdglover.com/youpassbutter-slack-bot-part-1/">Part 1</a> to get the context behind the app and the initial implementation.</p>
<p>In this post, we'll</p>
<ul>
<li>re-structure our Slash Commands and routes to get more functionality</li>
<li>handle our second requirement, namely generating dynamic memes and POSTing them back to Slack</li>
<li>package our Slash Commands as a Slack app, including the OAuth configuration needed for the "Add to Slack" button</li>
</ul>
<h2>Re-structuring our commands</h2>
<p>In <a href="http://alexdglover.com/youpassbutter-slack-bot-part-1/">Part 1</a>, we had a one-to-one mapping from our Slash Commands to our API endpoints. This made our app very clean and simple, but has one serious drawback; Slack only allows up to 25 slash commands per 'app.' We haven't even implemented our meme generation feature yet, so this is a dealbreaker for us.</p>
<p>Let's consolidate our image-specific Slash commands into one image-fetching Slash command, and we'll pass a second argument to the command to specify <em>which</em> image we want to return.</p>
<p>Since we're consolidating the Slash commands, we'll also need to consolidate our API endpoints to match. Fortunately, this is very easy. All of our Sinatra routes are basically key-value pairs, where the key is the route (e.g. /you-pass-butter) and the value is the URL to the image (e.g. https://media.giphy.com/media/Fsn4WJcqwlbtS/giphy.gif). Because of this, we can consolidate all of our routes into a Hash and write a single route to dynamically fetch and return the correct image.</p>
<pre class="lang:ruby decode:true" title="app.rb excerpt">COMMAND_IMAGE_MAPPING = {
  "nobody-exists-on-purpose" =&gt; "http://cdn.smosh.com/sites/default/files/2015/12/rickmorty15.jpg",
  "wriggety-wriggety-wrecked-son" =&gt; "http://www.reactiongifs.us/wp-content/uploads/2016/02/riggity_wrecked_son_rick_morty.gif",
  "planning-for-failure" =&gt; "http://2.media.dorkly.cvcdn.com/78/27/5e7374300a84037f2bdbc061f2e69211.jpg",
  ###
  # Several lines removed for brevity
  ###
  "not-today" =&gt; "http://i.imgur.com/kxHrcC5.gif"
}

post '/images' do
  image_response COMMAND_IMAGE_MAPPING[params['text']]
end</pre>
<p>But what if our users send an invalid argument to the Slash command? We should give a helpful response to the user, listing all of the valid arguments they could use with the Slash Command. We can do this by simply joining all of the keys of the Hash, and returning that as part of a helpful error message.</p>
<pre class="lang:ruby decode:true" title="app.rb excerpt">COMMAND_IMAGE_MAPPING = {
  "nobody-exists-on-purpose" =&gt; "http://cdn.smosh.com/sites/default/files/2015/12/rickmorty15.jpg",
  "wriggety-wriggety-wrecked-son" =&gt; "http://www.reactiongifs.us/wp-content/uploads/2016/02/riggity_wrecked_son_rick_morty.gif",
  "planning-for-failure" =&gt; "http://2.media.dorkly.cvcdn.com/78/27/5e7374300a84037f2bdbc061f2e69211.jpg",
  ###
  # Several lines removed for brevity
  ###
  "not-today" =&gt; "http://i.imgur.com/kxHrcC5.gif"
}

post '/images' do
  if COMMAND_IMAGE_MAPPING.key?(params['text'])
    image_response COMMAND_IMAGE_MAPPING[params['text']]
  else
    images = COMMAND_IMAGE_MAPPING.keys.sort.join(",\n  ")
    string_as_json_response "Cannot find that image #{params['text']}. Full list of images:\n  #{images}"
  end
end</pre>
<p>Let's add one more <a href="https://www.interaction-design.org/literature/book/the-glossary-of-human-computer-interaction/affordances">affordance</a> for our users. Let's add a second Slash Command, (/rm-list-images) and a corresdonding API route (/images/all) that just returns the full list of possible images.</p>
<pre class="lang:ruby decode:true " title="app.rb excerpt">post '/images/all' do
  images = COMMAND_IMAGE_MAPPING.keys.sort.join(",\n  ")
  string_as_json_response "Full list of images:\n  #{images}"
end</pre>
<p>Just for reference, here are what the two Slash Commands look like in the Slack UI:</p>
<p><a href="http://alexdglover.com/wp-content/uploads/2017/01/YouPassButter-Part2-SlackUI1.png"><img class="aligncenter size-medium wp-image-1055" src="{{ site.baseurl }}/assets/YouPassButter-Part2-SlackUI1-237x300.png" alt="YouPassButter-Part2-SlackUI1" width="237" height="300" /></a><a href="http://alexdglover.com/wp-content/uploads/2017/01/YouPassButter-Part2-SlackUI2.png"><img class="aligncenter size-medium wp-image-1056" src="{{ site.baseurl }}/assets/YouPassButter-Part2-SlackUI2-230x300.png" alt="YouPassButter-Part2-SlackUI2" width="230" height="300" /></a></p>
<p>Perfect, so our original image commands still work and we've made room for some new commands through our refactor.</p>
<h2>Dynamic Memes</h2>
<p>Images are all well and good, but we can do better. Let's add a feature to generate dynamic memes, so you can apply Rick and Morty humor to your 9-to-5.</p>
<p>I used <a href="http://version1.api.memegenerator.net/">the memegenerator.net API</a> and I highly recommend it. It's free and the authentication  is dirt simple (albeit with a cleartext password). All you need to do is sign up for an account on <a href="https://memegenerator.net/">the Meme Generator homepage.</a></p>
<p>In their API documentation, you'll notice that meme templates are referred to as 'generators' while an instance of a particular meme (with custom text) is just referred to as an instance. Now we <em>could</em> use the API to look up the generator ID based on the name of the meme, but people have used all sorts of garbage names like 'Rick Sanchez123.' Instead, I found a few memes I wanted to support in the Meme Generator UI, looked up the generator ID for that specific meme, and recorded it in a Ruby hash.</p>
<pre class="lang:ruby decode:true" title="app.rb excerpt">COMMAND_MEME_MAPPING = {
  "tiny-rick" =&gt; {
    "generatorID": 5200542,
    "displayName": "Tiny Rick"
  },
  "get-schwifty" =&gt; {
    "generatorID": 5841203,
    "displayName": "Rick Sanchez Schwifty"
  },
  "rick-shrug" =&gt; {
    "generatorID": 5733231,
    "displayName": "Rick Sanchez123"
  },
  "rick-scolding-morty" =&gt; {
    "generatorID": 6223262,
    "displayName": "Rick Sanchezzz"
  },
  "way-up-your-butt" =&gt; {
    "generatorID": 6063115,
    "displayName": "Rick Sanchez 2"
  },
  "get-your-shit-together" =&gt; {
    "generatorID": 6168409,
    "displayName": "mortygetyourshittogether"
  },
  "rick-and-morty-couch" =&gt; {
    "generatorID": 4875756,
    "displayName": "Rick and Morty Couch"
  },
  "i-just-got-bored" =&gt; {
    "generatorID": 4229669,
    "displayName": "Rick and Morty - I'm bored"
  },
  "show-me-what-you-got" =&gt; {
    "generatorID": 6873210,
    "displayName": "Show Me What You GotT"
  },
  "bird-person" =&gt; {
    "generatorID": 3687578,
    "displayName": "Bird Person Rick and Morty"
  },
  "look-morty" =&gt; {
    "generatorID": 5932800,
    "displayName": "Grandpa Rick"
  }
}</pre>
<p>In addition to the key, I also retained the original display name for reference later.</p>
<p>Generating a meme is fairly trivial. You just need to make an HTTP GET request to</p>
<pre class="lang:default decode:true ">http://version1.api.memegenerator.net/" +
"Instance_Create?username=#{MEME_GENERATOR_USERNAME}" +
"&amp;password=#{MEME_GENERATOR_PASSWORD}&amp;languageCode=en&amp;" +
"generatorID=#{generator_id}&amp;text0=#{text0}&amp;text1=#{text1}"</pre>
<p>where the generatorID is the ID of the meme you want to use, text0 is the top line of text, and text1 is the bottom line of text. To illustrate:</p>
<p><a href="http://alexdglover.com/wp-content/uploads/2017/01/YPB-Part2-Meme-Text-Placement.gif"><img class="aligncenter size-large wp-image-1060" src="{{ site.baseurl }}/assets/YPB-Part2-Meme-Text-Placement-1024x531.gif" alt="YPB-Part2-Meme-Text-Placement" width="616" height="319" /></a></p>
<p>OK, so now we just need another API route and Slash command to handle generating these memes on the fly. For our Slash Command, let's plan on sending those 3 pieces of information as a text argument. Because we'll be sending arbitrary strings of text for our dank memes, let's use '; ' (read: semi-colon, space) to separate those elements (e.g. "meme ID; top text; bottom text").</p>
<p>The other thing we have to consider before writing any code is timeouts. Slash Commands must get a valid HTTP response within three seconds, or they will throw a timeout error. We can't assume the Meme Generator's API or our app will be able to reliably return an image in three seconds. Instead of responding to the original HTTP request, our app will make an <strong>asynchronous</strong> request to the 'response_url' provided in the Slack payload once the meme generation completes.</p>
<p>With that in mind, let's start with building the API route in Sinatra:</p>
<pre class="lang:ruby decode:true " title="app.rb excerpt">post '/memes' do
  # Grab the entire text from the Slash Command and split the meme, 
  # top text, and bottom text from the text argument from Slack
  text_params = params['text'].split('; ')
  # Grab the response URL from the Slack HTTP call. This is the
  # Slack endpoint we'll post the result to
  response_url = params['response_url']

  # If the meme name arg is found in the meme hash...
  if COMMAND_MEME_MAPPING.key?(text_params[0])
    generator_id = COMMAND_MEME_MAPPING[text_params[0]][:generatorID]
    text0 = URI.encode(text_params[1])
    text1 = URI.encode(text_params[2])
    # generate memes
    response = HTTParty.get("http://version1.api.memegenerator.net/" +
      "Instance_Create?username=#{MEME_GENERATOR_USERNAME}" +
      "&amp;password=#{MEME_GENERATOR_PASSWORD}&amp;languageCode=en&amp;" +
      "generatorID=#{generator_id}&amp;text0=#{text0}&amp;text1=#{text1}")
    if response['success']
      post_image_to_response_url response_url, response['result']['instanceImageUrl']
      status 200
    else
      string_as_json_response "Error generating meme"
    end
  # If the meme is not found, return an error message and 
  # include full list of available memes
  else
    memes = COMMAND_MEME_MAPPING.keys.sort.join(",\n  ")
    string_as_json_response "Cannot find that meme #{text_params[0]}. Full list of available memes:\n  #{memes}"
  end

end</pre>
<p>New users may not know all of the meme options available, so let's also throw in an API route to list all of the available memes.</p>
<pre class="lang:ruby decode:true " title="app.rb excerpt">post '/memes/all' do
  memes = COMMAND_MEME_MAPPING.keys.sort.join(",\n  ")
  string_as_json_response "Full list of memes:\n  #{memes}"
end</pre>
<p>Finally, let's create a Slash Command for each of those endpoints in the Slack app management console:</p>
<p><a href="http://alexdglover.com/wp-content/uploads/2017/01/YouPassButter-Part2-SlackUI3.png"><img class="aligncenter size-large wp-image-1065" src="{{ site.baseurl }}/assets/YouPassButter-Part2-SlackUI3-783x1024.png" alt="YouPassButter-Part2-SlackUI3" width="616" height="806" /></a><a href="http://alexdglover.com/wp-content/uploads/2017/01/YouPassButter-Part2-SlackUI4.png"><img class="aligncenter size-large wp-image-1066" src="{{ site.baseurl }}/assets/YouPassButter-Part2-SlackUI4-798x1024.png" alt="YouPassButter-Part2-SlackUI4" width="616" height="790" /></a></p>
<p>Before we can test these new commands, we need to set the MEME_GENERATOR_USERNAME and MEME_GENERATOR_PASSWORD variables in a safe and secure way. First, let's set them as environment variables. In Heroku, this is as simple as executing:</p>
<pre class="lang:sh decode:true ">[user@host ~]$ heroku config:set MEME_GENERATOR_USERNAME=my_username
[user@host ~]$ heroku config:set MEME_GENERATOR_PASSWORD=my_password</pre>
<p>Now you can refer directly to those environment variables in-line, like this:</p>
<pre class="lang:ruby decode:true" title="app.rb excerpt">response = HTTParty.get("http://version1.api.memegenerator.net/" +
      "Instance_Create?username=#{ENV['MEME_GENERATOR_USERNAME']}" +
      "&amp;password=#{ENV['MEME_GENERATOR_PASSWORD']}&amp;languageCode=...</pre>
<p>Excellent, now we can test our command. Let's look at the GIF demo one more time to see it in action:</p>
<p><a href="http://alexdglover.com/wp-content/uploads/2017/01/YPB-Part2-Meme-Text-Placement.gif"><img class="aligncenter size-large wp-image-1060" src="{{ site.baseurl }}/assets/YPB-Part2-Meme-Text-Placement-1024x531.gif" alt="YPB-Part2-Meme-Text-Placement" width="616" height="319" /></a></p>
<h2>OAuth and "Publishing"</h2>
<p>In order to publish our Slash Command app on Slack's marketplace or share it with the "Add to Slack" button, our application needs to have an OAuth endpoint that initiates the user authentication to their team, thus authorizing our application to be installed into their Slack team.</p>
<p>In more complex applications (like real time chat bots) we need to store information sent to us in the OAuth request from Slack. In our simple app, we just need to respond to the OAuth request by POSTing to Slack's /api/oauth endpoint.</p>
<p>Let's start be defining our OAuth endpoint in Slack. Let's go back to <a href="https://api.slack.com/apps">https://api.slack.com/apps</a>, drill into our app by clicking the app name, and then click the "OAuth &amp; Permissions" on the left. Next, let's define our Redirect URL as 'https://&lt;your-domain&gt;/oauth' and save.</p>
<p><a href="http://alexdglover.com/wp-content/uploads/2017/01/YouPassButter-Part2-SlackUI5.png"><img class="aligncenter size-large wp-image-1070" src="{{ site.baseurl }}/assets/YouPassButter-Part2-SlackUI5-1024x516.png" alt="YouPassButter-Part2-SlackUI5" width="616" height="310" /></a></p>
<p>Next, let's click on the Basic Information tab, and capture the 'client ID' and 'client secret':</p>
<p><a href="http://alexdglover.com/wp-content/uploads/2017/01/YouPassButter-Part2-SlackUI6.png"><img class="aligncenter size-large wp-image-1071" src="{{ site.baseurl }}/assets/YouPassButter-Part2-SlackUI6-1024x558.png" alt="" width="616" height="336" /></a></p>
<p>Don't be an idiot and commit these to Git like I did. Keep these out of your repo, or put them in an environment variable file and add it to your .gitignore. If someone captured your client ID and secret, they could impersonate your application and wreak havoc.</p>
<p>Once we've got our redirect URL set up and gathered our OAuth client keys, we can construct an easy /oauth route in our Sinatra app. Our route should trigger a POST to the Slack /api/OAuth endpoint. If that post gets an HTTP 200 response, let's just post some simple HTML to show the app was successfully installed. If not, let's dump the response.body to determine the cause of the failure.</p>
<pre class="lang:ruby decode:true " title="app.rb excerpt">get '/oauth' do
  response = HTTParty.post("https://slack.com/api/oauth.access?client_id=#{ENV['SLACK_CLIENT_ID']}&amp;client_secret=#{ENV['SLACK_CLIENT_SECRET']}&amp;code=#{params['code']}")
  responseBody = JSON.parse(response.body)
  if responseBody['ok'] == true
    "&lt;h1&gt;You've just installed the YouPassButter Slack bot! Let's get riggety wrecked!!!&lt;/h1&gt;"
  else
    response.body
  end
end</pre>
<p>Much like before with our Meme Generator credentials, we have to set these Slack client credentials as environment variables so they can be securely consumed by our app without being in source control (Git).</p>
<pre class="lang:sh decode:true">[user@host ~]$ heroku config:set SLACK_CLIENT_ID=1234567890
[user@host ~]$ heroku config:set SLACK_CLIENT_SECRET=0987654321</pre>
<p>Alright, now that we have our OAuth endpoint set up, let's focus on packaging our app.</p>
<p>In order for people to install our app, we need to use either the <a href="https://api.slack.com/docs/slack-button">"Add to Slack" button</a> or publish it on the Slack App Directory. To get published to the App Directory, your app has to be approved by Slack, you have to provide a landing page for the app, and you need some basic customer support mechanisms in place, etc. In short, it's a non-trivial effort.</p>
<p>Instead, we can just create the "Add to Slack" button. The process is much simpler and there's less overhead. Hell, the code is even generated for you automatically just by browsing to <a href="https://api.slack.com/docs/slack-button">https://api.slack.com/docs/slack-button</a> (assuming you're already logged in). Let's do that now. Scroll down to the "Add the Slack Button" section, select your Slash Command app in the dropdown in the top right, and then check <em>only</em> the 'commands' checkbox:</p>
<p><a href="http://alexdglover.com/wp-content/uploads/2017/01/YPB-Part2-AddToSlackButton.gif"><img class="aligncenter size-large wp-image-1074" src="{{ site.baseurl }}/assets/YPB-Part2-AddToSlackButton-1024x579.gif" alt="YPB-Part2-AddToSlackButton" width="616" height="348" /></a></p>
<p>Once you've got the HTML copied, you can drop it into your blog, emails, or a landing page you maintain for the app. Here's the Add to Slack button for the YouPassButter app:</p>
<p><a href="https://slack.com/oauth/authorize?scope=commands&amp;client_id=122992570306.122925378483"><img src="{{ site.baseurl }}/assets/add_to_slack.png" srcset="https://platform.slack-edge.com/img/add_to_slack.png 1x, https://platform.slack-edge.com/img/add_to_slack@2x.png 2x" alt="Add to Slack" width="139" height="40" /></a></p>
<p>Here's a quick demo of a user clicking the button, authenticating to their Slack team, and getting a successful response from our /oauth endpoint:</p>
<p><a href="http://alexdglover.com/wp-content/uploads/2017/01/YPB-Part2-AddToSlack-Auth-Demo.gif"><img class="aligncenter size-large wp-image-1086" src="{{ site.baseurl }}/assets/YPB-Part2-AddToSlack-Auth-Demo-1024x616.gif" alt="YPB-Part2-AddToSlack-Auth-Demo" width="616" height="371" /></a></p>
<p>Now obviously our confirmation page is a little plain to say the least, but whatever, it works!</p>
<h2>Conclusion</h2>
<p>That's it for our YouPassButter Slack bot. It fetches images/gifs, generates memes, and can be easily installed by any Slack team. Now for a celebratory demo gif:</p>
<p><a href="http://alexdglover.com/wp-content/uploads/2017/01/YPB-Part2-Final-Demo-Gif.gif"><img class="aligncenter size-full wp-image-1084" src="{{ site.baseurl }}/assets/YPB-Part2-Final-Demo-Gif.gif" alt="YPB-Part2-Final-Demo-Gif" width="566" height="340" /></a></p>
