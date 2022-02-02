---
layout: archive
permalink: life
title: "Life"

author_profile: true
---

{% assign posts = site.categories.life %}
{% for post in posts %}
  {% include custom-archive-single.html type=entries_layout %}
{% endfor %}