---
layout: archive
permalink: book
title: "Book"

author_profile: true
---

{% assign posts = site.categories.book %}
{% for post in posts %}
  {% include custom-archive-single.html type=entries_layout %}
{% endfor %}