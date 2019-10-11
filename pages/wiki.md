---
layout: page
title: Wikis
description: stay hungry and stay foolish
keywords: 维基, wiki
comments: false
menu: 维基
permalink: /wiki/
---

> Archive some links

<ul class="listing">
{% for wiki in site.wiki %}
{% if wiki.title != "Wiki Template" %}
<li class="listing-item"><a href="{{ site.url }}{{ wiki.url }}">{{ wiki.title }}</a></li>
{% endif %}
{% endfor %}
</ul>
