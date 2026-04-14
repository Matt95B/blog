---
layout: page
title: Blog posts
permalink: /posts
---

{% assign tags = "" | split: "" %}
{% for post in site.posts %}
  {% for tag in post.tags %}
    {% unless tags contains tag %}
      {% assign tags = tags | push: tag %}
    {% endunless %}
  {% endfor %}
{% endfor %}
{% assign tags = tags | sort %}

<div class="tag-filter" style="margin-bottom: 2rem;">
  <button class="tag-btn active" data-tag="all" onclick="filterTag('all', this)">All</button>
  {% for tag in tags %}
    <button class="tag-btn" data-tag="{{ tag }}" onclick="filterTag('{{ tag }}', this)">{{ tag }}</button>
  {% endfor %}
</div>

<div class="posts-grid" id="posts-container">
  {% for post in site.posts %}
    <article class="post-card" data-tags="{{ post.tags | join: ',' }}">
      <div class="article-box">
        {% if post.image %}
          <div class="article-image">
            <a href="{{ post.url | relative_url }}">
              <img src="{{ post.image | relative_url }}" alt="{{ post.title }}">
            </a>
          </div>
        {% endif %}
        <div class="article-content">
          <h2 class="article-title">
            <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
          </h2>
          <p class="article-excerpt">{{ post.description | default: post.excerpt | strip_html | truncatewords: 30 }}</p>
          <div class="article-info">
            <span class="article-date">{{ post.date | date: "%d %b %Y" }}</span>
            <div class="article-tags">
              {% for tag in post.tags %}
                <a href="{{ site.baseurl }}/tag/{{ tag }}" class="article-tag">{{ tag }}</a>
              {% endfor %}
            </div>
          </div>
        </div>
      </div>
    </article>
  {% endfor %}
</div>

<style>
  .tag-filter {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
  }
  .tag-btn {
    padding: 6px 14px;
    border: 1px solid var(--border-color, #ddd);
    border-radius: 20px;
    background: transparent;
    cursor: pointer;
    font-size: 0.85rem;
    transition: all 0.2s;
  }
  .tag-btn:hover,
  .tag-btn.active {
    background: var(--brand-color, #000);
    color: #fff;
    border-color: var(--brand-color, #000);
  }
</style>

<script>
  function filterTag(tag, btn) {
    document.querySelectorAll('.tag-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');

    document.querySelectorAll('.post-card').forEach(card => {
      if (tag === 'all') {
        card.style.display = '';
      } else {
        const tags = card.dataset.tags.split(',');
        card.style.display = tags.includes(tag) ? '' : 'none';
      }
    });
  }
</script>