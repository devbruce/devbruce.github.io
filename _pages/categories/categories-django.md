---
title: "Django Posts"
permalink: /categories/django/
layout: archive
author_profile: true
---

{% assign posts = site.categories.Django | sort:'date' | reverse %}

{% for post in posts %}
    {% include archive-single.html %}
{% endfor %}
