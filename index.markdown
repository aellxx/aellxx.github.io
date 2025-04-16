---
layout: home
title: ""
---

# Welcome to my brain space

## 💻 General Tech Stuff
<ul>
  {% for post in site.swe %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

## 📊 AWS Stuff
<ul>
  {% for post in site.aws %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

