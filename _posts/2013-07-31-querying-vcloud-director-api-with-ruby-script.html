---

title: Querying vCloud Director API with Ruby Script
date: 2013-07-31 20:28:09.000000000 -05:00



categories:
- Utilities And Other Useful Things
tags:
- api
- chef
- net::http
- ruby
- script
- vcloud
- vcloud director
meta:
  _edit_last: '1'
  _s2mail: 'yes'
  _wpas_done_all: '1'
  _wpas_skip_1477652: '1'
  _wpas_skip_1477650: '1'
---
<p>Well it's been awhile since I've posted since I started working on <a title="HUJ" href="http://www.hireupjob.com/" target="_blank">HireUpJob</a>, but this one's good. I recently had a use case where I needed to get the virtual hardware version of a client machine provisioned through vCloud Director. Now, that information is not accessible anywhere on the client machine, so I had to look to the vCloud Director API for it. The process for querying the API also had to be automated for future provisions, so it had to be scripted. Enough back story and context, lets look at the ruby code.</p>
<pre class="lang:ruby decode:true ">#Need to import a few ruby packages
require 'net/http'
require 'uri'
require 'openssl'
#Here we are ignoring any issues that may arise with SSL certificates
OpenSSL::SSL::VERIFY_PEER = OpenSSL::SSL::VERIFY_NONE

#main fetch function takes an initial URL to fetch from and a limit for number of redirects to follow
def fetch(uri_str, limit = 10)
  raise ArgumentError, 'HTTP redirect too deep' if limit == 0

  #Parse URL into URI object
  uri = URI.parse(uri_str)
  #Create new Net::HTTP object
  http = Net::HTTP.new(uri.host, uri.port)
  #Set the http object to use SSL
  http.use_ssl = true
  #Ignore issues with SSL certs
  http.verify_mode = OpenSSL::SSL::VERIFY_NONE
  #Start the http session
  http.start()
  #Create an HTTP POST request to /api/sessions to authenticate
  req = Net::HTTP::Post.new('/api/sessions')
  #Set HTTP basic auth variables of username and password
  req.basic_auth '&lt;your_username&gt;@&lt;your_org&gt;', '&lt;your_password&gt;'
  req.add_field 'Accept:', 'application/*+xml;version=1.5'
  #Send the request we created earlier, and capture the HTTP response
  response = http.request(req)
  #Start case to handle various HTTP responses
  case response
  #If authentication is successful, a token is returned in the response header. So if we get an HTTP 200 response from the vCloud server, then we can grab the "x-vcloud-authorization" token from the header 
  when Net::HTTPSuccess     then 
	authKey = response.header['x-vcloud-authorization']
  #if we get an HTTPfound instead, we are probably getting redirected and should try to follow it. You may need to tweak this section to suit your use case
  when Net::HTTPFound		then
        #call the main fetch function again and decrement the overall redirect limit 
	fetch(response['location'], limit - 1)
  #If we get an HTTP redirection, we are definitely getting redirected and should follow it
  when Net::HTTPRedirection then fetch(response['location'], limit - 1)
  else
    #something went wrong - spit out whatever the response error is
    puts response.error!
  end

  #Set the headers as a variable to be re-used later, including the authorization token we grabbed earlier
  headers = {
    'Accept' =&gt; 'application/*+xml;version=1.5',
	'x-vcloud-authorization' =&gt; "#{authKey}"
  }

  #Build a new request, this time an HTTP GET, to grab a value from the API. Supply both the API call and the headers variable we just put together
  req = Net::HTTP::Get.new('/api/vApp/vm-insert-your-alphanumeric-UUID-here/virtualHardwareSection', headers)
  #send the new request and grab the response again
  response = http.request(req)
  case response
  when Net::HTTPSuccess     then 
        #In my case, the value I wanted was returned in the XML body that was returned, so if the server responds with HTTP 200 I just grab the entire response body and pass it to a variable
	result = response.body()
  #same as previous
  when Net::HTTPFound		then 
	fetch(response['location'], limit - 1)
  #same as previous
  when Net::HTTPRedirection then fetch(response['location'], limit - 1)
  else
    puts response.error!
  end

  #Finally, build one more request to send an HTTP DELETE call to /api/sessions to kill your session
  req = Net::HTTP::Delete.new('/api/sessions', headers)
  http.request(req)

  #Again, in my case, I wanted to confirm that the VM was using a specific virtual hardware version (vmx-08), so I did a quick string match to confirm this
  result.include?('&lt;vssd:VirtualSystemType&gt;vmx-08&lt;/vssd:VirtualSystemType&gt;')

end

print fetch('https://&lt;your_vcloud_server&gt;:443')</pre>
<p>If you're using my code more or less verbatim and everything executes properly, you should get a simple "true" or "false" as output. Now, this script is for a fairly specific use case, but I think the real potential value here is to use this enable Opscode Chef (written in Ruby) to communicate with vCloud Director. I'll be sure to post again as I develop some use cases.</p>
<p>In case you have a very different use case but need some help, here are some of the pages I referred to for writing this script:</p>
<ul>
<li>Exploring the vCloud REST API - <a href="http://blogs.vmware.com/vsphere/2012/03/exploring-the-vcloud-rest-api-part-1.html" target="_blank">Part I</a> and <a href="http://blogs.vmware.com/vsphere/2012/03/exploring-the-vcloud-rest-api-part-2.html" target="_blank">Part II</a></li>
<li><a href="http://www.vmware.com/support/vcd/doc/rest-api-doc-1.5-html/" target="_blank">vCloud Director REST API Reference</a></li>
<li><a href="http://stackoverflow.com/questions/6934185/ruby-net-http-following-redirects" target="_blank">Ruby - Net::HTTP - following redirects</a></li>
</ul>
<p>Hope this helps some of you out there and let me know if you have any questions.</p>
<p>&nbsp;</p>
