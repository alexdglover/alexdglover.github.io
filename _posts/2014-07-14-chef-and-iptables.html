---

title: Chef and iptables
date: 2014-07-14 08:30:23.000000000 -05:00



categories:
- Questions for the Community
- Utilities And Other Useful Things
tags:
- chef
- configuration management
- devops
- iptables
meta:
  _edit_last: '1'
  _s2mail: 'yes'
  _wpas_done_all: '1'
  _wpas_skip_1477652: '1'
  _wpas_skip_1477650: '1'
---
<p>Like many DevOps professionals before me, I needed a way to manage iptables programmatically through Chef. Before I realized that Opscode had written a cookbook for just this, I had already spent some time building my own. The cookbook written by Opscode is very clever and allows you to apply a set of iptables rules with a single line in another recipe. The only problem is that it is predicated on you creating 'templates' of iptables policies you want to apply in advance, and you can only apply those templates. You can't just supply a port number or part of a rule. Additionally, their cookbook wasn't compatible with <a href="http://www.vagrantup.com/" target="_blank">Vagrant</a> because of the ruby binary wasn't in the paths used in the cookbook.</p>
<p>Short version - I wrote my own iptables cookbook and augmented the Opscode iptables cookbook, and I wanted to share.</p>
<h2>Changes to the Opscode Cookbook</h2>
<p>Basically, the cookbook functionality is unchanged save for two parts. First, in addition to the previous method for invoking iptables, you can now invoke it by setting the <span style="color: #000000;"><span class="o">[</span><span class="s1">'iptables'</span><span class="o">][</span><span class="s1">'roles'</span><span class="o">]  attribute to one or more roles (comma delimited). There is a new 'default.rb' attribute file and the default recipe has been modified slighly to support this. Second, the default recipe has been modified to detect the path to the ruby binary so as to be compatible with Vagrant boxes instead of being hardcoded.</span></span></p>
<p>You can check out all of the code on <a href="https://github.com/alexdglover/iptables" target="_blank">Github</a>.</p>
<h2>My iptables Cookbook</h2>
<p>My requirements were a little less generic, and so my iptables cookbook is a little more isolated than the Opscode version. Specifically, I didn't want to affect any existing iptables rules or chains, so my cookbook injects new chains that should be specific for the servers application (hence App-INPUT and App-OUTPUT chains). Unfortunately, today my cookbook only supports ports to be specified via attributes and only works on Enterprise Linux OS's. Having seen and played with Opscode version, I'll have to refactor mine to support Debian Linux versions as well as non-attribute driven usage.</p>
<p><!--more See the full cookbook source--></p>
<p>To see the full cookbook or to clone, check out the repo on <a href="https://github.com/alexdglover/inline-iptables" target="_blank">Github</a>. Otherwise the bulk of the logic is in the default recipe, which you can check out here:</p>
<p>&nbsp;</p>
<pre class="lang:default decode:true">#
# Cookbook Name:	inline-iptables
# Recipe: 		default
# Author:		Alex D Glover (alex@alexdglover.com)
# Description:		A recipe to manage iptables without impacting existing 
#			iptables rules or chains, and allowing individual
#			inbound/outbound ports as well as port ranges to be 
#			managed with programmatic ease.
# Dependencies:		attributes/default.rb
# Usage:		Set the ["inline-iptables"]["listen_ports"] and/or
#			["inline-iptables"]["outbound_ports"] attributes at the
#			node or role level



# Grab the comma separated list of ports that need to be open.
# Keep in mind these attribute could be set by a role, recipe, or node override
listen_ports      = node["inline-iptables"]["listen_ports"]
outbound_ports    = node["inline-iptables"]["outbound_ports"]

# Boolean variable to track if we a change has been made
iptables_modified = false

# Debug friendly logging
Chef::Log.info &lt;&lt;-EOS

Entering ondemand_base::iptables_manager {
  listen_ports        = #{listen_ports}
  outbound_ports      = #{outbound_ports}
}
EOS

# Read the current iptables data
iptables_content = File.read("/etc/sysconfig/iptables")

# If the list of ports is the empty string, do nothing
unless listen_ports == ""
  
  # We are creating a new iptables chain called App-INPUT; if it already
  # exists, don't create it again
  unless iptables_content.include?(":App-INPUT - [")
    execute "create new INPUT chain" do
      command "iptables -N App-INPUT;"
    end
    iptables_modified = true
  end
  
  # Break our port list into an array
  listen_ports_array = listen_ports.split(',')

  # For each port that needs to be opened, insert the corresponding rule
  # into our chain, but only if doesn't exist already
  listen_ports_array.each do |port|
    unless iptables_content.include?("-A App-INPUT -p tcp -m tcp --dport #{port} -j ACCEPT")
      execute "add input rule to iptables for port #{port}" do
        command "iptables -I App-INPUT -p tcp --dport #{port} -j ACCEPT"
      end
      iptables_modified = true
    end
  end

  # Connect our App-INPUT chain to the generic INPUT chain
  unless iptables_content.include?("-A INPUT -j App-INPUT")
    execute "connect App-INPUT to INPUT chain" do
      command "iptables -I INPUT -j App-INPUT"
    end
    iptables_modified = true
  end
  
  # Connect our App-INPUT chain to the generic FORWARD chain
  unless iptables_content.include?("-A FORWARD -j App-INPUT")
    execute "connect App-INPUT to FORWARD chain" do
      command "iptables -I FORWARD -j App-INPUT"
    end
    iptables_modified = true
  end

else
  Chef::Log.info "No listen ports specified to be opened"
end

# If the list of ports is the empty string, do nothing
unless outbound_ports == ""
  
  # We are creating a new iptables chain called App-OUTPUT; if it already
  # exists, don't create it again
  unless iptables_content.include?(":App-OUTPUT - [")
    execute "create new OUTPUT chain" do
      command "iptables -N App-OUTPUT;"
    end
    iptables_modified = true
  end

  # Break our port list into an array
  outbound_ports_array = outbound_ports.split(',')

  # For each port that needs to be opened, insert the corresponding rule
  # into our chain, but only if doesn't exist already
  outbound_ports_array.each do |port|
    execute "add input rule to iptables for port #{port}" do
      command "iptables -I App-OUTPUT -p tcp --dport #{port} -j ACCEPT"
      only_if {!iptables_content.include?("-A App-OUTPUT -p tcp -m tcp --dport #{port} -j ACCEPT")}
    end
    iptables_modified = true
  end
  
  # Connect our App-OUTPUT chain to the generic OUTPUT chain
  unless iptables_content.include?("-A OUTPUT -j App-OUTPUT")
    execute "connect App-OUTPUT to OUTPUT chain" do
      command "iptables -I OUTPUT -j App-OUTPUT"
    end
    iptables_modified = true
  end

else
Chef::Log.info "No outbound ports specified to be opened"
end

if iptables_modified
  
  execute "save updated iptables" do
    command "/etc/init.d/iptables save"
  end

  service "iptables" do
    action :restart
    ignore_failure true
  end
else
  Chef::Log.info "No changes made, not restarting iptables"
end
</pre>
<p>Would greatly appreciate any feedback/criticism from the Chef community. Thanks for reading.</p>
