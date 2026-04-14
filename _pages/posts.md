---
layout: page
title: Blog posts
permalink: /posts
---
<style>
  .tag-filter {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 2rem;
  }
  .tag-btn {
    padding: 6px 16px;
    border: 1px solid var(--border-color);
    border-radius: 20px;
    background: transparent;
    cursor: pointer;
    font-size: 0.85rem;
    color: inherit;
    transition: background 0.2s, color 0.2s, border-color 0.2s;
  }
  .tag-btn:hover,
  .tag-btn.active {
    background: var(--brand-color);
    color: #fff;
    border-color: var(--brand-color);
  }
</style>

{% assign all_tags = "" | split: "" %}
{% for post in site.posts %}
  {% for tag in post.tags %}
    {% unless all_tags contains tag %}
      {% assign all_tags = all_tags | push: tag %}
    {% endunless %}
  {% endfor %}
{% endfor %}
{% assign all_tags = all_tags | sort %}

<div class="tag-filter">
  <button class="tag-btn active" data-tag="all" onclick="filterTag('all', this)">All</button>
  {% for tag in all_tags %}
    <button class="tag-btn" data-tag="{{ tag }}" onclick="filterTag('{{ tag }}', this)">{{ tag }}</button>
  {% endfor %}
</div>

<div class="articles" id="posts-container">
  {% for post in site.posts %}
    <div class="article col col-12 animate post-card" data-tags="{{ post.tags | join: '|' }}">
      <div class="article__inner">
        <div class="article__content">
          <h2 class="article__title">
            <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
          </h2>
          <p class="article__excerpt">{{ post.description | default: post.excerpt | strip_html | truncatewords: 30 }}</p>
          <div class="article__bottom">
            <div class="article__meta">
              <a href="/about/" class="article__author-image">
                <img class="lazy" alt="{{ site.author }}" src="{{ site.baseurl }}/images/me_headshot.png">
              </a>
              <div class="article__info">
                <a href="/about/" class="article__author-link">{{ site.author }}</a>
                <time class="article__date" datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%d %b %Y" }}</time>
              </div>
            </div>
            <div class="article-tags__box">
              {% for tag in post.tags %}
                <a href="{{ site.baseurl }}/tag/{{ tag }}" class="article__tag">{{ tag }}</a>
              {% endfor %}
            </div>
          </div>
        </div>
      </div>
    </div>
  {% endfor %}
</div>

<script>
  function filterTag(tag, btn) {
    document.querySelectorAll('.tag-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    document.querySelectorAll('.post-card').forEach(card => {
      if (tag === 'all') {
        card.style.display = '';
      } else {
        const tags = card.dataset.tags.split('|');
        card.style.display = tags.includes(tag) ? '' : 'none';
      }
    });
  }
</script>