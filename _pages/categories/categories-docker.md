---
title: "Docker Posts"
permalink: /categories/docker/
layout: archive
author_profile: true
---

{% assign posts = site.categories.Docker | sort:'date' | reverse %}

{% for post in posts %}
    {% include archive-single.html %}
{% endfor %}
