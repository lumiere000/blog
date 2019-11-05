---
layout: article
title: css hover image button using div id
cover: /img/css.webp
tags: css website coding desgin
category: css
---

First, use div id to identify a button area :

```html
<div id="button01"><img src="https://yourdomain/button_area.webp" width="150 "></div>
```

Then, use css to change image, first one is normal status, second one is hover status :  

```css
#button01 {
	width: 150px;
	background: url(https://domain/yourimage.webp) no-repeat;
	background-size: 150px;
}

#button01:hover {
	width: 150px;
	background: url(https://domain/yourimage-on.webp) no-repeat;
	background-size: 150px;
}
```
