---
layout: posts
title: css hover image button using div id
image: /img/css.png
tags: css website coding desgin
category: css
---

First, use div id to identify a button area :

```html
<div id="button01"><img src="https://yourdomain/button_area.png" width="150 "></div>
```

Then, use css to change image, first one is normal status, second one is hover status :  

```css
#button01 {
	width: 150px;
	background: url(https://domain/yourimage.png) no-repeat;
	background-size: 150px;
}

#button01:hover {
	width: 150px;
	background: url(https://domain/yourimage-on.png) no-repeat;
	background-size: 150px;
}
```
