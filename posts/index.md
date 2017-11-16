---
layout: default
title: Posts
permalink: /posts
---
<ul class="entries">
  {% for post in site.posts %}

  <li>
    <a href="{{ post.url }}">
    <img src="/posts/{{ post.image }}"/> <br>
      <h3>{{ post.title }}</h3>
    </a>
  </li>

  {% endfor %}
</ul>
