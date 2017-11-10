---
layout: default
title: Posts
---
<ul class="entries">
  {% for post in site.posts %}
 
  <li>
    <a href="/blog{{ post.url }}">
      <img src="{{ post.image }}"/>
      <h3>{{ post.title }}</h3>
    </a>
  </li>
 
  {% endfor %}
</ul>
