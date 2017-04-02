---
title: "Blog"
---

{% for post in site.posts %}
  - [{{ post.title }}]({{ post.url }})  
    {{ post.excerpt | strip_html }}
{% endfor %}

