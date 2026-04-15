---
layout: default
title: Blog posts
permalink: /posts
---
<style>
  .tag-filter {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 2rem;
    padding: 0 16px;
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

{% include author.html %}

<div class="container">
  <div class="tag-filter">
    <button class="tag-btn active" data-tag="all" onclick="filterTag('all', this)">All</button>
    {% for tag in all_tags %}
      <button class="tag-btn" data-tag="{{ tag }}" onclick="filterTag('{{ tag }}', this)">{{ tag }}</button>
    {% endfor %}
  </div>

  <div class="row" id="posts-container">
    <div class="col col-12">
      {% for post in site.posts %}
        <div class="post-card animate" data-tags="{{ post.tags | join: '|' }}">
          {% include article-content.html %}
        </div>
      {% endfor %}
    </div>
  </div>
</div>

<script>
  const buttons = document.querySelectorAll('.tag-btn');
  const posts = document.querySelectorAll('.post-card');

  function filterPosts(tag) {
    posts.forEach(post => {
      const tags = post.getAttribute('data-tags');

      if (tag === 'all' || tags.includes(tag)) {
        post.style.display = 'block';
      } else {
        post.style.display = 'none';
      }
    });

    buttons.forEach(btn => {
      btn.classList.remove('active');
      if (btn.getAttribute('data-tag') === tag) {
        btn.classList.add('active');
      }
    });
  }

  // 👇 READ URL PARAM
  const params = new URLSearchParams(window.location.search);
  const tagFromUrl = params.get('tag');

  if (tagFromUrl) {
    filterPosts(tagFromUrl);
  }

  // 👇 BUTTON CLICK HANDLING
  buttons.forEach(button => {
    button.addEventListener('click', () => {
      const tag = button.getAttribute('data-tag');

      // update URL without reload
      const url = new URL(window.location);
      url.searchParams.set('tag', tag);
      window.history.pushState({}, '', url);

      filterPosts(tag);
    });
  });
</script>