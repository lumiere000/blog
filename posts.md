---
layout: default
title: Posts
permalink: /posts
---

<ul class="entries">
  {% for post in site.posts %}

  <li>
  <div style="float: left;">
    <a href="{{ post.url }}">
    <img src="{{ post.image }}"/>
</div>
      <h3>{{ post.title }}</h3>
      <div align="right">{{ post.date | date: '%B %-d, %Y'}}</div>

    </a>
  </li>

  {% endfor %}

</ul>
