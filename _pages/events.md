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

  /* Force article__content to fill full width for event cards */
  .article[data-year] .article__content {
    display: block;
    width: 100%;
    box-sizing: border-box;
  }

  /* Single image layout — CSS grid, text left, image right */
  .event__inner--with-image {
    display: grid;
    grid-template-columns: 1fr 250px;
    gap: 1.5rem;
    width: 100%;
    box-sizing: border-box;
  }

  /* No image — just full width text */
  .event__inner {
    display: block;
    width: 100%;
  }

  .event__body {
    min-width: 0;
    text-align: left;
  }

  /* Single image — right column, top aligned */
  .event__image-single {
    width: 250px;
    align-self: start;
  }
  .event__image-single img {
    width: 100%;
    height: auto;
    border-radius: 6px;
    display: block;
  }

  /* Multiple images — full width below text, centred */
  .event__images-multi {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    gap: 12px;
    margin-top: 1rem;
    width: 100%;
    box-sizing: border-box;
  }
  .event__images-multi img {
    width: 250px;
    height: auto;
    border-radius: 6px;
    object-fit: cover;
  }

  /* Mobile */
  @media (max-width: 640px) {
    .event__inner--with-image {
      display: block;
    }
    .event__image-single {
      width: 100%;
      margin-top: 1rem;
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
      <button class="filter-btn filter-year active" data-year="all" onclick="filterEvents(this, '.filter-year')">All</button>
      {% for year in all_years %}
        <button class="filter-btn filter-year" data-year="{{ year }}" onclick="filterEvents(this, '.filter-year')">{{ year }}</button>
      {% endfor %}
    </div>
    <div class="event-filter-group">
      <span class="event-filter-label">Type:</span>
      <button class="filter-btn filter-type active" data-type="all" onclick="filterEvents(this, '.filter-type')">All</button>
      {% for type in all_types %}
        <button class="filter-btn filter-type" data-type="{{ type }}" onclick="filterEvents(this, '.filter-type')">{{ type }}</button>
      {% endfor %}
    </div>
  </div>

  <!-- Events list -->
  <div class="row" id="events-container">
    <div class="col col-12">
      {% for event in sorted_events %}
        {% assign img_count = event.images | size %}
        <div class="article col col-12 animate"
             data-year="{{ event.date | date: '%Y' }}"
             data-type="{{ event.type }}">
          <div class="article__inner">
            <div class="article__content">

              <div class="event__inner {% if img_count == 1 %}event__inner--with-image{% endif %}">
                <div class="event__body">
                  <h2 class="article__title">{{ event.name }}</h2>
                  <p>
                    <time class="article__date" datetime="{{ event.date | date: '%Y-%m-%d' }}">{{ event.date | date: "%d %B %Y" }}</time><br>
                    {{ event.type }}<br>
                    {% if event.city %}{{ event.city }}<br>{% endif %}
                    Role: <strong>{{ event.role }}</strong><br>
                    {% if event.platform %}
                      Platform:
                      {% if event.platform_url %}
                        <a href="{{ event.platform_url }}" target="_blank" rel="noopener">{{ event.platform }}</a>
                      {% else %}
                        {{ event.platform }}
                      {% endif %}
                    {% endif %}
                  </p>
                </div>

                {% if img_count == 1 %}
                  <div class="event__image-single">
                    <img src="{{ event.images[0] }}" alt="{{ event.name }}" loading="lazy">
                  </div>
                {% endif %}
              </div>

              {% if img_count > 1 %}
                <div class="event__images-multi">
                  {% for img in event.images %}
                    <img src="{{ img }}" alt="{{ event.name }}" loading="lazy">
                  {% endfor %}
                </div>
              {% endif %}

            </div>
          </div>
        </div>
      {% endfor %}
    </div>
  </div>

</div>

<script>
  function filterEvents(btn, group) {
    document.querySelectorAll(group).forEach(b => b.classList.remove('active'));
    btn.classList.add('active');

    const activeYear = document.querySelector('.filter-year.active').dataset.year;
    const activeType = document.querySelector('.filter-type.active').dataset.type;

    document.querySelectorAll('.article[data-year]').forEach(card => {
      const yearMatch = activeYear === 'all' || card.dataset.year === activeYear;
      const typeMatch = activeType === 'all' || card.dataset.type === activeType;
      card.style.display = (yearMatch && typeMatch) ? '' : 'none';
    });
  }
</script>
