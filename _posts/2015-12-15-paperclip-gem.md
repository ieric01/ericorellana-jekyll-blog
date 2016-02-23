---
layout: post
title:  "Paperclip Gem for Image Uploading"
date:   2015-12-15
---
### Introduction:

So, I’ve been working on my second Rails project, Bucketlistr, at Flatiron. The purpose of Bucketlistr is that you can save events/things you’ve always wanted to do, which will be stored in your bucketlist. Even cooler, you are informed of the great deals associated with that bucketlist item.

### Problem:

If a user completes an activity from their bucketlist, we want to give the user an option to upload an image associated with that activity. So how do we accomplish this?

### Solution:

We used the paper clip gem. Click on PAPERCLIP GEM TO LEARN MORE ABOUT IT.

### WorkFlow:

1.Install ImageMagick, which is the software that reads and write images in a variety of formats, which Paperclip must have access to:
If you’re on Mac OS X, you’ll want to run the following with Homebrew:

{% highlight ruby %}
brew install imagemagick
{% endhighlight %}

If you are dealing with pdf uploads or running the test suite, you’ll also need GhostScript to be installed. On Mac OS X, you can also install that using Homebrew:

{% highlight ruby %}
brew install gs
{% endhighlight %}

To ensure that imagemagick has been installed, run which convert in the command line, which will return you the path where that utility is installed. For example, it might return /usr/local/bin/convert.

Then, in your environment config file, let Paperclip know to look there by adding that directory to its path.  Add this line to config/environments/development.rb

{% highlight ruby %}
Paperclip.options[:command_path] = "/usr/local/bin/.
{% endhighlight %}

2.In your gemfile, include:

{% highlight ruby %}
gem "paperclip", "~> 4.2"
{% endhighlight %}

3.Now indicate in the appropriate model, that it has an attached file/image… Also, you can add the styles of the images you would like. For example, you can have a thumbnail of the image or a medium/large size of the image, which you can then use to your liking in your application…. For example:

{% highlight ruby %}
class Todo < ActiveRecord::Base
  has_attached_file :image, :styles => { :medium => "300x300>", :thumb => "100x100>" }
  validate_attachment_content_type :image, :content_type => /\Aimage\/.*\Z/
end
{% endhighlight %}

4.Now, you have to create a migration in which you add the image column to the model’s table in the database.

You can do  “rails generate paperclip todo image”

or

{% highlight ruby %}
class AddImageColumnsToTodo < ActiveRecord::Migration
  def self.up
    add_attachment :todos, :image
  end

  def self.down
    remove_attachment :todos, :image
  end
end
{% endhighlight %}
In your database table for that model, you will now have additional columns as such:

{% highlight ruby %}
t.string “new_image_file_name”
t.string “new_image_content_type”
t.integer “new_image_file_size”
t.datetime “new_image_updated_at”
{% endhighlight %}

5.Finally, you have to display the file_field to upload an image in your corresponding view and the most important thing to remember with file uploads is that the form’s encoding MUST be set to “multipart/form-data”. If you forget to do this the file will not be uploaded. This can be done by passing :multi_part => true as an HTML option. ….

{% highlight ruby %}
## View Page
 <%= form_for @todo, :url => '', :html => { :multipart => true } do |form| %>
   <%= form.label "name" %>
   <%= form.text_field :name, {:value => params['name'], :size => 60 } %>
   <%= form.label "Description" %>
   <%= form.text_area :description %>
   <%= form.file_field :new_image %>
   <%= form.submit "Create Todo" %>
   <% end %>
 <% end %>
{% endhighlight %}
