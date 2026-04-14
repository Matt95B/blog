---
layout: default
title: Events
description: List of events I attended and participated in over the years
permalink: /events
---
<style>
  /* Filters */
  .event-filters {
    display: flex;
    flex-direction: column;
    gap: 12px;
    margin-bottom: 2rem;
    padding: 0 16px;
  }
  .event-filter-group {
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    gap: 8px;
  }
  .event-filter-label {
    font-weight: 600;
    font-size: 0.85rem;
    margin-right: 4px;
    min-width: 36px;
  }
  .filter-btn {
    padding: 5px 14px;
    border: 1px solid var(--border-color);
    border-radius: 20px;
    background: transparent;
    cursor: pointer;
    font-size: 0.82rem;
    color: inherit;
    transition: background 0.2s, color 0.2s, border-color 0.2s;
  }
  .filter-btn:hover,
  .filter-btn.active {
    background: var(--brand-color);
    color: #fff;
    border-color: var(--brand-color);
  }

  /* Event card */
  .event-card {
    padding: 1.5rem 1rem 0;
  }
  .event-card__inner {
    display: flex;
    flex-direction: column;
  }
  .event-card__inner--with-image {
    flex-direction: row;
    align-items: flex-start;
    gap: 1.5rem;
  }
  .event-card__body {
    flex: 1;
  }
  .event-card__title {
    margin: 0 0 0.4rem;
    font-size: 1.1rem;
  }
  .event-card__meta {
    font-size: 0.85rem;
    color: var(--text-alt-color, #666);
    margin-bottom: 0.4rem;
    display: flex;
    flex-wrap: wrap;
    gap: 4px;
    align-items: center;
  }
  .event-card__dot {
    opacity: 0.4;
  }
  .event-card__role {
    margin: 0.2rem 0;
    font-size: 0.9rem;
  }
  .event-card__link {
    margin: 0.2rem 0;
    font-size: 0.9rem;
  }
  .event-card__content {
    margin-top: 0.5rem;
    font-size: 0.9rem;
  }

  /* Single image — right side */
  .event-card__image-single {
    flex-shrink: 0;
    width: 220px;
  }
  .event-card__image-single img {
    width: 100%;
    height: auto;
    border-radius: 6px;
    display: block;
  }

  /* Multiple images — below, centred */
  .event-card__images-multi {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    gap: 12px;
    margin-top: 1rem;
  }
  .event-card__images-multi img {
    height: 180px;
    width: auto;
    border-radius: 6px;
    object-fit: cover;
  }

  .event-card__divider {
    margin: 1.5rem 0 0;
    border: none;
    border-top: 1px solid var(--border-color);
  }

  /* Mobile: stack single-image layout */
  @media (max-width: 640px) {
    .event-card__inner--with-image {
      flex-direction: column;
    }
    .event-card__image-single {
      width: 100%;
    }
  }
</style>

{% include author.html %}

<div class="container">

  {% assign all_years = "" | split: "" %}
  {% assign all_types = "" | split: "" %}
  {% assign sorted_events = site.events | sort: "date" | reverse %}

  {% for event in sorted_events %}
    {% assign y = event.date | date: "%Y" %}
    {% unless all_years contains y %}
      {% assign all_years = all_years | push: y %}
    {% endunless %}
    {% unless all_types contains event.type %}
      {% assign all_types = all_types | push: event.type %}
    {% endunless %}
  {% endfor %}
  {% assign all_years = all_years | sort | reverse %}
  {% assign all_types = all_types | sort %}

  <!-- Filters -->
  <div class="event-filters">
    <div class="event-filter-group">
      <span class="event-filter-label">Year:</span>
      <button class="filter-btn filter-year active" data-year="all" onclick="filterEvents()">All</button>
      {% for year in all_years %}
        <button class="filter-btn filter-year" data-year="{{ year }}" onclick="filterEvents()">{{ year }}</button>
      {% endfor %}
    </div>
    <div class="event-filter-group">
      <span class="event-filter-label">Type:</span>
      <button class="filter-btn filter-type active" data-type="all" onclick="filterEvents()">All</button>
      {% for type in all_types %}
        <button class="filter-btn filter-type" data-type="{{ type }}" onclick="filterEvents()">{{ type }}</button>
      {% endfor %}
    </div>
  </div>

  <!-- Events list -->
  <div class="row" id="events-container">
    <div class="col col-12">
      {% for event in sorted_events %}
        {% assign img_count = event.images | size %}
        <div class="event-card"
             data-year="{{ event.date | date: '%Y' }}"
             data-type="{{ event.type }}">
          <div class="event-card__inner {% if img_count == 1 %}event-card__inner--with-image{% endif %}">

            <div class="event-card__body">
              <h3 class="event-card__title">{{ event.name }}</h3>
              <div class="event-card__meta">
                <time>{{ event.date | date: "%d %B %Y" }}</time>
                <span class="event-card__dot">·</span>
                <span class="event-card__type">{{ event.type }}</span>
                {% if event.city %}
                  <span class="event-card__dot">·</span>
                  <span class="event-card__city">{{ event.city }}</span>
                {% endif %}
              </div>
              <p class="event-card__role">Role: <strong>{{ event.role }}</strong></p>
              {% if event.link %}
                <p class="event-card__link">
                  Platform: <a href="{{ event.link.url }}" target="_blank" rel="noopener">{{ event.link.label }}</a>
                </p>
              {% endif %}
              {% if event.content != "" %}
                <div class="event-card__content">{{ event.content }}</div>
              {% endif %}
            </div>

            {% if img_count == 1 %}
              <div class="event-card__image-single">
                <img src="{{ event.images[0] }}" alt="{{ event.name }}" loading="lazy">
              </div>
            {% endif %}

          </div>

          {% if img_count > 1 %}
            <div class="event-card__images-multi">
              {% for img in event.images %}
                <img src="{{ img }}" alt="{{ event.name }}" loading="lazy">
              {% endfor %}
            </div>
          {% endif %}

          <hr class="event-card__divider">
        </div>
      {% endfor %}
    </div>
  </div>

</div>

<script>
  function filterEvents() {
    const activeYear = document.querySelector('.filter-year.active').dataset.year;
    const activeType = document.querySelector('.filter-type.active').dataset.type;

    document.querySelectorAll('.event-card').forEach(card => {
      const yearMatch = activeYear === 'all' || card.dataset.year === activeYear;
      const typeMatch = activeType === 'all' || card.dataset.type === activeType;
      card.style.display = (yearMatch && typeMatch) ? '' : 'none';
    });
  }

  // Make filter buttons exclusive within their group
  document.querySelectorAll('.filter-btn').forEach(btn => {
    btn.addEventListener('click', function() {
      const group = this.classList.contains('filter-year') ? '.filter-year' : '.filter-type';
      document.querySelectorAll(group).forEach(b => b.classList.remove('active'));
      this.classList.add('active');
    });
  });
</script>