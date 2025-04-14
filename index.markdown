---
layout: home
title: ""
---

# Welcome to my brain space

## 💻 General tech stuff
<ul>
  {% for post in site.swe %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

