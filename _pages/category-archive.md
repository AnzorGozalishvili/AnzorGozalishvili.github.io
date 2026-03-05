---
title: "Posts"
layout: archive
permalink: /categories/
author_profile: true
---

{% include group-by-array collection=site.posts field="categories" %}

{% for category in group_names %}
  {% assign posts_in_category = group_items[forloop.index0] %}
  <h2 id="{{ category | slugify }}" class="archive__subtitle">{{ category }}</h2>
  {% for post in posts_in_category %}
    {% include archive-single.html %}
  {% endfor %}
{% endfor %}
