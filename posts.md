---
layout: default
title: Posts
permalink: /posts
---

<ul class="entries">
  {% for post in site.posts %}

  <li>
    <a href="{{ post.url }}">
    <p><img src="{{ post.image }}"/>
      <h3>{{ post.title }}</h3></p>
      <div align="right">{{ post.date | date: '%B %-d, %Y'}}</div>

    </a>
  </li>

  {% endfor %}

</ul>
