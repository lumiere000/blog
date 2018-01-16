---
layout: default
title: Geek
permalink: /
---

 A geek does not have to be smart. We interested in things that others are not interested in, we know a lot about our interests.


 <div class="blog-index">  
   {% assign post = site.posts.first %}
   {% assign content = post.content %}
   {% include post_detail.html %}
 </div>
