---
layout: post
title: "Fix Chef cookbook templates without forking"
date: 2014-04-03 23:25:12 -0400
comments: true
categories: chef ruby devops
---

One of the key features with Chef is reusability, which is why many teams have started using Application Cookbooks (as [described here](http://alluvium.com/blog/2013/05/03/the-application-cookbook-pattern-berkshelf-and-team-chef-workflow/)).  In my experience, application cookbooks are a game changer and make development, testing, and deployment simple.  That being said, app cookbooks create their own set of issues as well...

Our team has worked to use community cookbooks when possible to build out an environment, but there are often times a cookbook may be missing a simple line or an attribute required for your use case.  When that happens you have a number of options:

 * Fork the community cookbook and make your own.
 * Put in a pull request for the feature you require.
 * Put in a FileEdit hack to insert configs after an "include_recipe" converge
 * Override the template.

Now all of these options are completely feasible, however lets break them down.  

##Forking the community cookbook and make it your own

Now this was the early way many Chef's used while the community was young.  Unless you have time to spend upkeeping your cookbooks, many can be left to the community to manage.  Forking them makes it challenging to track upstream changes and usually get off track.

## Put in a pull request for the feature you require.
Pull requests are great and I think this is the best option, however, lets be honest, there's many a time I would love to put in a PR but need the issue fixed immediately. In that case, your PR and fork generally are put into use immediately and not through the community, resulting in the same issue as above.

## Put in a FileEdit hack to insert configs after an "include_recipe" converge

This solution that avoids two of the above issues, but also causes some of it's own.

Lets say you're building a tomcat based app and have used the [community cookbook](https://github.com/opscode-cookbooks/tomcat).  Now when you load the WAR into your project, you want to mount it on the base rather than www.example.com/appname.  Of course there are many ways to solve this including tools like nginx but rewrites cause other issues with sessions and cookies... so lets mount it the way our developer requests.  Unfortunately the cookbook does not provide functionality to edit the docBase Context or insert it as an attribute.  Lets insert it by hand:  

``` ruby /recipes/default.rb
ruby_block "Fix Tomcat" do
  block do
    puts "Lets add a line to fix tomcat docBase"
    file = Chef::Util::FileEdit.new(node[:tomcat][:config_dir] + "/server.xml")

    file.insert_line_after_match(
    "<\!-- Access log processes all example.",
    "\n        # Added Via Chef:\n        <Context docBase=\"appname\" path=\"\" reloadable=\"true\"/>\n        <!-- Access log processes all example."
    )

    file.write_file
  end
end
```

Now that works but if your do a full run of chef, you'll notice that your tomcat will restart and the file will first be cleansed back to it's original state, then the edit will be readded.  Well that's painful and ugly.

## Override the template.
This is my preferred method and the best of both worlds.  Instead of forking a cookbook or hacking in a change, we take the existing template, copy it into our app cookbook, insert our configs and reference our edited template like such:

``` ruby /recipes/default.rb
begin
  r = resources(:template => "#{node[:tomcat][:config_dir]}/server.xml")
  r.cookbook "appcookbook"
rescue Chef::Exceptions::ResourceNotFound
  Chef::Log.warn "could not find template to override!"
end
```
Some argue that this is very similar to a full fork of a cookbook however I believe it's the best solution for the job.  To accompany this change (and always), you should lock the version of the included cookbook in your metadata.rb file to prevent the community cookbook from breaking if the template is changed in the future.

Any alternative ideas or suggestions are welcome (if I can ever get disqus to show up)!
