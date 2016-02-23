---
layout: post
title:  "Embedded Ruby"
date:   2015-10-07
---

ERB which stands for "Embedded Ruby" is simply awesome!

ERB is a feature of Ruby which allows you to embed, for example HTML files with ruby code.

This means you can generate a template, and instead of having many html files that each say:

Hello Robbie OR Hello Bob OR Hello Jane, you can have one file/template that does the tedious work for you! Like this:

{% highlight ruby %}
<!DOCTYPE html>
<html>
  <body>
    <p>Hello, <%= user.first_name %></p>
  </body>
</html>
{% endhighlight %}

The <strong> <%= %> </strong> tag signs indicates that the enclosed code is an expression, and that it will be substituted with the result of the code.

The <strong> <% %> </strong> tag signs without the equal sign, also known as <strong> scriplets </strong>, is used mostly for loops or conditional logic, for example in our ‘INTRO TO ERB LAB’, we used this to iterate through each movie and output their url’s.

{% highlight ruby %}
<% Movies.all.each do |movie| %>
  <li><a href="<%= movie.url %>">
  <%= movie.title %></a></li>
<% end %>

{% endhighlight %}

NOTE: The scriptlets themselves will not produce any text themselves, only the tag signs with the equal signs will.

After we have the template, we need a way to access and input the data in it. The template has embedded ruby code but it is not really valuable to us, unless, we can actually do something with it. This is when we make our template an ERB object. We are now able to call methods on it, such as erb.result(binding), which will display the results of the erb file with our desired input.

{% highlight ruby %}
erb = ERB.new(File.read('./lib.templates/movie.html.erb'))
Movie.all.each do |movie|
  file_name = movie.url
  File.open("./_site/#{directory_name}/#{file_name}", "w+") do |f|
    f.write(erb.result(binding))
  end
end
{% endhighlight %}

I know binding may seem confusing but in this case all binding does is that it acts like a time stopper. It provides erb with the scope in which the code is being executed, meaning we have access to the variables and current movie object in this example. I imagine binding, as a hardworking man who stops everything and will not move on until the current template is done and filled in with all the necessary information!
