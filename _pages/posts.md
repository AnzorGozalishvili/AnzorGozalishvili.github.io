---
title: "Posts"
permalink: /posts/

layout: archive
header:
    image: /images/pages/mona-eendra-NZNFY_g6ong-unsplash.jpg
---
<h3 class="archive__subtitle">Posts</h3>

{% for post in site.posts %}
  {% include archive-single.html %}
{% endfor %}
