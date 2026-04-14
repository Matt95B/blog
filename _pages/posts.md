---
layout: page
title: Blog posts
---
<style>
.blog-filters {
    position: sticky;
    top: 0;
    background: white;
    z-index: 100;
}

.filter-btn {
    border: none;
    background: #f1f3f5;
    padding: 8px 14px;
    margin: 4px;
    border-radius: 20px;
    cursor: pointer;
    font-size: 0.8rem;
    transition: all 0.2s ease;
}

.filter-btn:hover {
    background: #e0e0e0;
}

.filter-btn.active {
    background: #007bff;
    color: #fff;
}

.post-tags {
    margin-top: 10px;
}

.tag {
    display: inline-block;
    font-size: 0.7rem;
    background: #eef2f7;
    padding: 4px 10px;
    border-radius: 12px;
    margin-right: 5px;
}
</style>   

<!-- TAG FILTER -->
<section class="blog-filters">
  <div class="container text-center">

    <button class="filter-btn active" data-tag="all">All</button>

    {% assign all_tags = site.tags %}
    {% for tag in all_tags %}
      <button class="filter-btn" data-tag="{{ tag[0] | downcase }}">
        {{ tag[0] }}
      </button>
    {% endfor %}

  </div>
</section>

<!-- POSTS (same layout as homepage) -->
{% for post in site.posts %}
<section class="post-preview"
         data-tags="{{ post.tags | join: ' ' | downcase }}">

  <div class="container">
    <div class="row">
      
      <div class="col-lg-8 col-lg-offset-2 col-md-10 col-md-offset-1">

        <a href="{{ post.url }}">
          <h2 class="post-title">
            {{ post.title }}
          </h2>

          {% if post.subtitle %}
          <h3 class="post-subtitle">
            {{ post.subtitle }}
          </h3>
          {% endif %}
        </a>

        <p class="post-meta">
          {{ post.date | date: "%B %d, %Y" }}
          {% if post.read_time %}
            • {{ post.read_time }} min read
          {% endif %}
        </p>

        <p>
          {{ post.excerpt | strip_html | truncate: 160 }}
        </p>

        <!-- TAG DISPLAY -->
        <div class="post-tags">
          {% for tag in post.tags %}
            <span class="tag">{{ tag }}</span>
          {% endfor %}
        </div>

      </div>

    </div>
  </div>

</section>
<hr>
{% endfor %}

<script>
document.addEventListener("DOMContentLoaded", function () {
  const buttons = document.querySelectorAll(".filter-btn");
  const posts = document.querySelectorAll(".post-preview");

  buttons.forEach(button => {
    button.addEventListener("click", () => {

      buttons.forEach(btn => btn.classList.remove("active"));
      button.classList.add("active");

      const selectedTag = button.getAttribute("data-tag");

      posts.forEach(post => {
        const tags = post.getAttribute("data-tags");

        if (selectedTag === "all") {
          post.style.display = "block";
        } else {
          if (tags.includes(selectedTag)) {
            post.style.display = "block";
          } else {
            post.style.display = "none";
          }
        }
      });

    });
  });
});
</script>