---
title: "HTML&CSS Posts"
permalink: /categories/hc/
layout: archive
author_profile: true
---

{% assign posts = site.categories.HC | sort:'date' | reverse %}

{% for post in posts %}
    {% include archive-single.html %}
{% endfor %}
