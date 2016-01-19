---
layout: page
title: Archive
---

## Blog Posts

{% for post in site.posts %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ post.url }})
{% endfor %}

## Tech
 {% for post in site.categories['tech'] limit:1 %}
    <a href="{{ post.url }}">{{ post.title }}</a>
  {% endfor %}

## Fitness
  {% for post in site.categories['fitness'] limit:1 %}
    <a href="{{ post.url }}">{{ post.title }}</a>
  {% endfor %}