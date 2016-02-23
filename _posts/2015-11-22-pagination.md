---
layout: post
title:  "Page Pagination"
date:   2015-11-22
---
### Introduction:

At the Flatiron School, project week finally arrived. The mission was to create a fully functional application in seven days, using rails. It was time to put into use what I’ve learned over the past 12 weeks. It was similar to being given a set of lego pieces, each with its own purpose and use, and creating something out of it. Although, the road seemed steeped, I knew this was a golden opportunity to learn more about rails and evaluate my strengths and weaknesses. On this project, I teamed up with two of my buddies, and named it “AnimeChase”#

### Problem:

After setting up the models, associations, and logic of  ‘AnimeChase,” we encountered a slight challenge. We got the animes along with with its title, image, and description to display, but there were too many on one page. Our question was “Is there a way to limit the number of animes that will display on a page?”

### Solution:

There was certainly a solution, and it is called pagination. I headed over to rubygems.org and encountered the “will-paginate” gem. Click on Will-Pagination Documentation to learn more about it.

### WorkFlow:

- Require gem ‘will_paginate’ in your gemfile.
- Then run bundle install in your terminal to install any dependencies.
- In your controller, which in my case was the ‘AnimesController,’ I called paginate in the following method. To give you the context on what’s going on, the user makes a GET request and in params, the key is ‘genre’ and the value is for example, comedy. I look up the genre in the database and call on its animes, which is returned as an array of objects, which I named @animes_in_genre_array.

{% highlight ruby %}
def animes_in_genre
  @animes_in_genre_array = Genre.find_by('name' => params['genre']).animes
  @animes_in_genre_array = @animes_in_genre_array.paginate(:page => params[:page], :per_page => 10)
  render "animes_in_genre"
end
{% endhighlight %}

I want to paginate the animes listed under its appropriate genre. For example, if you clicked on the comedy category, I wanted only 10 animes to display on a single page. To do this, I called the paginate method on @animes_in_genre_array:

{% highlight ruby %}
@animes_in_genre_array = @animes_in_genre_array.paginate(:page => params[:page], :per_page => 10)
{% endhighlight %}

In params[:page], I get the page number and in :per_page I indicate I want 10 animes to display on a page, which is arbitrary.

Finally, in my animes_in_genre.html.erb file, I state

{% highlight ruby %}
## render page links in the view:
<%= will_paginate @animes_in_genre_array %>
{% endhighlight %}

### Result:

<img class="img-responsive" src="/assets/img/page-pagination.jpg">

