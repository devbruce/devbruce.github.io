---
title: "Ruby Posts"
permalink: /categories/ruby/
layout: archive
author_profile: true
---

{% assign posts = site.categories.Ruby | sort:'date' | reverse %}

{% for post in posts %}
    {% include archive-single.html %}
{% endfor %}
