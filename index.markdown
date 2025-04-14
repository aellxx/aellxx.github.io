---
layout: home
---

## 💻 general swe stuff
<ul>
  {% for post in site.swe %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

