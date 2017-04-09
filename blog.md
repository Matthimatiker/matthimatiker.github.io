---
title: "Blog"
---

## Blog ##

{% for post in site.posts %}
  - <time datetime="{{ post.date | date: '%Y-%m-%d' }}">{{ post.date | date_to_string }}</time>: [{{ post.title }}]({{ post.url }})  
    {{ post.excerpt | strip_html }}
{% endfor %}

