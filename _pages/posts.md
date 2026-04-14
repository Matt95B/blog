---
layout: page
title: Blog posts
---

{% include author.html %}

<div class="container">
  <div class="row">
    <div class="col col-12">
    {% if site.posts.size > 0 %}
        {% include article-content.html %}
      {% endfor %}
    {% endif %}
    </div>
  </div>
</div>