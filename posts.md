---
layout: default
title: Posts
permalink: /posts
---

<ul class="entries">
  {% for post in site.posts %}

<div class="container">
<nav>
  <li>
  <ul>
    <a href="{{ post.url }}">
    <img src="{{ post.image }}"/> <br>
  </ul>
  <article>
      <h3>{{ post.title }}</h3>
  </article>

    </a>
  </li>
</nav>
</div>
<div align="right">{{ post.date | date: '%B %-d, %Y'}}</div>
  {% endfor %}

</ul>
