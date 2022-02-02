---
layout: archive
permalink: investment
title: "Investment"

author_profile: true
---

{% assign posts = site.categories.investment %}
{% for post in posts %}
  {% include custom-archive-single.html type=entries_layout %}
{% endfor %}