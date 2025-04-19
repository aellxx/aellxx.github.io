---
layout: default
---
HELLO
<div class="category-container">
  <h1>{{ page.title }}</h1>
  
  {% if page.parent %}
    <p>Parent category: <a href="/{{ page.parent | downcase }}/">{{ page.parent }}</a></p>
  {% endif %}

  
  {{ content }}
  
  {% if page.parent %}
    <h2>Posts</h2>
    <ul>
      {% for post in site.categories[page.category] %}
        {% if post.categories contains page.parent %}
          <li>
            <a href="{{ post.url }}">{{ post.title }}</a>
            <span class="post-date">{{ post.date | date: "%B %d, %Y" }}</span>
          </li>
        {% endif %}
      {% endfor %}
    </ul>
  {% else %}
    {% assign subcategories = site.category | where: "parent", page.category %}
    {% if subcategories.size > 0 %}
      <h2>Subcategories</h2>
      <ul class="subcategories">
        {% for subcategory in subcategories %}
          <li><a href="{{ subcategory.permalink }}">{{ subcategory.title }}</a></li>
        {% endfor %}
      </ul>
    {% endif %}
    
    <h2>Posts</h2>
    <ul>
      {% for post in site.categories[page.category] %}
        <li>
          <a href="{{ post.url }}">{{ post.title }}</a>
          <span class="post-date">{{ post.date | date: "%B %d, %Y" }}</span>
        </li>
      {% endfor %}
    </ul>
  {% endif %}
</div>

HELLO