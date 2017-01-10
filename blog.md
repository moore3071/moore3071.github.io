---
layout: page
temporary_removal_title: blog
permalink: /blog/
---

<h1> Blogging time</h1>
{% for post in site.posts %}
  {% if post.categories contains 'blogging'%}
    * {{ post.date | date_to_string }} &raquo; [ {{ post.title}} ]({{ post.url }})
  {% endif %}
{% endfor %}
