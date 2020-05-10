---
layout: post
title:  "MyBatis example"
date:   2020-05-10 22:21:53 +0100
categories: jekyll update
---

Whenever you want to create a application ypu have to persist data somewhere. Most serious
functions need a proper DBMS for resolve this problem, but how we can handel a OOP entities
with relation table together. There is no silver bullet to do this. The already existing solutions
is ORM frameworks as Hibernate or persistence frameworks as MyBatis. What is the difference between of
them?

* OMR - it is complex solution which provide a way to map and manage persistent entity. It's means 
it not just map tables to objects but also mange a objects life cycle. The `EntityManager` play a 
key role in that solution. It decided when to gt object from cache or put it there, how
to flush cache entities to DB and build a queries for that.

* persistent mapper - it more simple solution that just provide a easiest way to map you query result set 
to a entity. 

As you see `persitent mapper` is more simple solution. There are not a lot of magic under the hood in it. 
You write you own query and than just map it in OP entity. And MyBatis is a persistent mapper. What is
benefits of that approach?

* you can write queries with a lot of complexity and it will be transparent for you
* there is no complex life cycle behind this solution so it easy to maintain
* mapping of result set data is implemented in one way an it much easy that do it with `jdbcTemplate` solution

So now let's move a little bit dipper and write some code

To use MyBatis you have to add a maven  

You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

Jekyll requires blog post files to be named according to the following format:

`YEAR-MONTH-DAY-title.MARKUP`

Where `YEAR` is a four-digit number, `MONTH` and `DAY` are both two-digit numbers, and `MARKUP` is the file extension representing the format used in the file. After that, include the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
