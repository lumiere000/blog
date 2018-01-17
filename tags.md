---
layout: default
title: tags
permalink: /tags
---

<ul>
  {% for tags in site.tags %}
    <li>{{ tags }}</li>
  {% endfor %}
</ul>
