---
layout: page
title: Blog posts
---

<section class="blog-hero">
  <div class="container text-center">
    <h1>All Posts</h1>
    <p>Browse everything or filter by topic</p>
  </div>
</section>

<section class="blog-filters">
  <div class="container text-center">

    <button class="filter-btn active" data-filter="all">All</button>

    {% assign categories = site.categories %}
    {% for category in categories %}
      <button class="filter-btn" data-filter="{{ category[0] | downcase }}">
        {{ category[0] }}
      </button>
    {% endfor %}

  </div>
</section>

<section class="blog-list">
  <div class="container">

    <div class="blog-grid" id="blogGrid">
      {% for post in site.posts %}
        <article class="blog-card"
                 data-category="{{ post.category | downcase }}">
          <a href="{{ post.url }}">

            {% if post.image %}
            <div class="blog-card-image">
              <img src="{{ post.image }}" alt="">
            </div>
            {% endif %}

            <div class="blog-card-content">
              <span class="blog-category">{{ post.category }}</span>
              <h2>{{ post.title }}</h2>

              <p class="blog-excerpt">
                {{ post.excerpt | strip_html | truncate: 140 }}
              </p>

              <div class="blog-meta">
                <span>{{ post.date | date: "%B %d, %Y" }}</span>
                {% if post.read_time %}
                <span>{{ post.read_time }} min read</span>
                {% endif %}
              </div>
            </div>

          </a>
        </article>
      {% endfor %}
    </div>

  </div>
</section>