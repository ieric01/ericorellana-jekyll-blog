---
layout: post
title:  "Params: Permit vs. Require...What's the difference?"
date:   2016-01-20 09:28:53
---

Mass sql injection, is an insertion of a sql query via an input field. In order to protect our application from these attacks, we use params. Params, is an instance of ActionController::Parameters, and has the methods require and permit.

### So what exactly does require and permit do ?

The permit method will not raise an error if a permitted attribute is not found, and will return the actual hash value.

Lets create our own params, by making an instance of ActionController::Parameters

{% highlight ruby %}
params = ActionController::Parameters.new(name: "eric", hobby: "juggling")
=> {"name"=>"eric", "hobby"=>"juggling"}

#Using the permit method

params.permit(:hobby)
=> {"hobby"=>"juggling"}

#Passing in an attribute that is missing

params.permit(:name, :address)
=> {"name"=>"eric"}
{% endhighlight %}

As you see above, if we use permit and pass in an attribute that is missing, the permit method does not throw an error and will only return what was found.

On the other hand,

The require method ensures that a specific parameter is present, if not, the require method raises an error. The require method does not return a hash but the value of the specified hash key. For example, using the same example above:

{% highlight ruby %}
params = ActionController::Parameters.new(name: "eric", hobby: "juggling")
=> {"name"=>"eric", "hobby"=>"juggling"}

#Using the require method

params.require(:name)
=> "eric"

#Passing in an attribute that is missing
params.require(:address)

=> ActionController::ParameterMissing: param is missing or the value is empty: address
Finally, lets use the require method and permit method on nested params…
{% endhighlight %}

Here we make a new instance of params, with the top level key being “book”

{% highlight ruby %}
params = ActionController::Parameters.new( "book"=>
 {"title"=>"Botanical Life",
 "description"=>"Exploring the different species of plants",
 "author_attributes"=>{"author_name"=>"James Godwin"}})
{% endhighlight %}

It's important to remember that the permit method only allows scalar values such as strings, numbers, date, or time, but not hashes or arrays

{% highlight ruby %}
# permit method only accepts scalar values
#No hashes or arrays accepted

#permitting book and title attribute wrong way
params.permit(:book, :title)

=> {}

#permitting book and title attribute right way

params.permit(book: [:title])
=> {"book"=>{"title"=>"Botanical Life"}}
{% endhighlight %}

Since the top level key is book and its value is a hash, we have to indicate that by putting the attributes in an array.

In our first try, we get an empty hash as if we didn’t permit anything at all. In our second try, we point the nested attributes in an array, and we get back the appropriate values in a hash.

Using require, is different in that it only returns the nested hash and not the parent one(book).

{% highlight ruby %}
params = ActionController::Parameters.new( "book"=>
 {"title"=>"Botanical Life",
 "description"=>"Exploring the different species of plants",
 "author_attributes"=>{"author_name"=>"James Godwin"}})

#Comparing require and permit

#Using only permit method without require

params.permit(book: [:title, :description, author_attributes: [:author_name]])
=> {"book"=>{"title"=>"Botanical Life", "description"=>"Exploring the different species of plants", "author_attributes"=>{"author_name"=>"James Godwin"}}}

#Using require method and permit method

params.require(:book).permit(:title, :description, author_attributes: [:author_name]])
=> {"title"=>"Botanical Life", "description"=>"Exploring the different species of plants", "author_attributes" => {"author_name" =>"James Godwin"}}

#If I just require book, it will return the actual value, which in this case the book key is pointing to a nested hash

params.require(:book)
=> {"title"=>"Botanical Life", "description"=>"Exploring the different species of plants", "author_attributes"=>{"author_name"=>"James Godwin"}}
{% endhighlight %}

As shown above when we use the require method we do not get back the book key, since require only returns back the actual value which in this case is a hash, and the permit method then returns the permitted attributes.

### Overall:

Its neat to understand the difference between require and permit. Permit is like saying hey i’m giving you list of people permission to join the party, if you don’t come its okay I will not cause chaos by putting out an error.

On the other hand, require will not allow anyone to come if you do not possess the required key. It will also yell out an error if a key is missing.
