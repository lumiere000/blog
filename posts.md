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
      <h3>{{ post.title }}<div align="right"> {{ post.date | date: '%B %-d, %Y'}}</div></h3>

    </a>
  </li>

  {% endfor %}

</ul>
