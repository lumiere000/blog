---
layout: default
title: Posts
permalink: /posts
---

<ul class="entries">
  {% for post in site.posts %}

  <li>
    <a href="{{ post.url }}">
    <img src="{{ post.image }}"/>
      {{ post.title }}
      <div align="right">{{ post.date | date: '%B %-d, %Y'}}</div>

    </a>
  </li>

  {% endfor %}

</ul>