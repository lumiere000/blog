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
      <h3>{{ post.title }}</h3>
      <div align="right">{{ post.date | date: '%B %-d, %Y'}}</div>

    </a>
  </li>

  {% endfor %}

<div class="result"><img src="'{{ post.image }}'"height="50" width="50"  class="result-img"><div class="result-body"><a href="'+store[ref].link+'" class="{{ post.title }}">'+store[ref].title+'</a><div class="post-date small">'+store[ref].category+' &times; '+store[ref].date+'</div><p>'+store[ref].excerpt+'</p></div>

</ul>
