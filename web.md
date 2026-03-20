---
layout: default
title: Web
---

# Web Challenges

{% raw %}
{% for post in site.categories.web %}
  - [{{ post.title }}]({{ post.url }})
{% endfor %}
{% endraw %}