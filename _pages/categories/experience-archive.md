---
layout: archive
permalink: experience
title: "Experience"

author_profile: true
---

{% assign posts = site.categories.experience %}
{% for post in posts %}
  {% include custom-archive-single.html type=entries_layout %}
{% endfor %}