---
layout: page
title: Archive
---
{% for post in site.posts %}
_{{ post.date | date: "%B %d, %Y" }}_<br/>
[{{ post.title }}]({{post.url}})<br/>
{% endfor %}
