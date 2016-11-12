---
layout: page
title: Hey, buddy, what you're looking for ?
tagline: ""
---
{% include JB/setup %}

## Sample Posts

Novembre 13th, 2016.

Some notes I leave here
As a side note, this blog is still in pure ALPHA mode :)

Be more than indulgent, please.
I just copied some notes to share them - and to be sure they are somewhere on Internet ;)


<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>



##### This blog is running on Jekyll/bootstrap with jekyllbootstrap
I use the bootswatch theme.
This theme is still unfinished. If you'd like to be added as a contributor, [please fork](http://github.com/dbtek/jekyll-bootstrap-3)!

<!--

Read [Jekyll Quick Start](http://jekyllbootstrap.com/usage/jekyll-quick-start.html)

[Jekyll Bootstrap](http://jekyllbootstrap.com)

-->
