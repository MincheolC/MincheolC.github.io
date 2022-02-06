---
layout: archive
permalink: /os
title: "Operating System"

author_profile: true
---

{% assign posts = site.categories.os %}
{% for post in posts %}
  {% include custom-archive-single.html type=entries_layout %}
{% endfor %}