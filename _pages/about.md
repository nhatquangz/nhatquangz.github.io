---
title: ""
permalink: /about/
layout: single
author_profile: true
---

Hi, I'm Quang — an iOS developer. On this blog I will share my learning, tip and tricks, tools, and problems I found along the way.

<h2>Recent posts</h2>
{% for post in site.posts limit:5 %}
    {% include single-post.html %}
{% endfor %}