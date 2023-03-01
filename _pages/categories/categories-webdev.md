---
title: "Web Development Posts"
permalink: /categories/webdev/
layout: archive
author_profile: true
---

{% assign posts = site.categories.webdev | sort:'date' | reverse %}

{% for post in posts %}
    {% include archive-single.html %}
{% endfor %}
