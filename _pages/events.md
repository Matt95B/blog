---
layout: page
title: Events
description: List of events I attended and participated in over the years
---

<div class="events-filter-wrapper">
  <h3 id="filterToggle" class="filter-toggle">
    <span id="filterIcon">▶</span> Filter events
  </h3>

  <div id="filterContent" class="events-filter collapsed">
    <label>
      Year:
      <select id="yearFilter">
        <option value="all">All</option>

        {% assign years = "" | split: "," %}
        {% for event in site.events %}
          {% assign year = event.date | date: "%Y" %}
          {% unless years contains year %}
            {% assign years = years | push: year %}
          {% endunless %}
        {% endfor %}

        {% assign years = years | sort | reverse %}
        {% for y in years %}
          <option value="{{ y }}">{{ y }}</option>
        {% endfor %}
      </select>
    </label>

    <label>
      Type:
      <select id="typeFilter">
        <option value="all">All</option>
        {% assign types = site.events | map: "type" | uniq | sort %}
        {% for t in types %}
          <option value="{{ t | downcase | replace: ' ', '-' }}">
            {{ t }}
          </option>
        {% endfor %}
      </select>
    </label>
  </div>
</div>

{% assign events = site.events | sort: "date" | reverse %}

{% for event in events %}
<section
  class="event-item"
  data-year="{{ event.date | date: '%Y' }}"
  data-type="{{ event.type | downcase | replace: ' ', '-' }}">

  {% assign image_count = event.images | size %}

  {% if image_count == 1 %}
    <!-- TEXT LEFT / IMAGE RIGHT -->
    <div class="events-container">
      <div class="events-text">
        <h3>{{ event.name }}</h3>

        <p>
          <time datetime="{{ event.date | date: '%Y-%m-%d' }}">
            {{ event.date | date: "%d %B %Y" }}
          </time><br>
          {{ event.type }}<br>
          {% if event.city %}{{ event.city }}<br>{% endif %}
          Role: <strong>{{ event.role }}</strong><br>
        {% if event.platform %}
          Platform:
          {% if event.platform_url %}
            <a href="{{ event.platform_url }}">{{ event.platform }}</a>
          {% else %}
            {{ event.platform }}
          {% endif %}
        </p>
        {% endif %}
      </div>

      <div class="events-image">
        <img
          src="{{ event.images[0] }}"
          alt="{{ event.name }}"
          loading="lazy">
      </div>
    </div>

  {% elsif image_count > 1 %}
    <!-- TEXT FULL WIDTH / IMAGES BELOW -->
    <div class="events-text">
      <h3>{{ event.name }}</h3>

      <p>
        <time datetime="{{ event.date | date: '%Y-%m-%d' }}">
          {{ event.date | date: "%d %B %Y" }}
        </time><br>
        {{ event.type }}<br>
        {% if event.city %}{{ event.city }}<br>{% endif %}
        Role: <strong>{{ event.role }}</strong><br>
      {% if event.platform %}
        Platform:
        {% if event.platform_url %}
          <a href="{{ event.platform_url }}">{{ event.platform }}</a>
        {% else %}
          {{ event.platform }}
        {% endif %}
      </p>
      {% endif %}
    </div>

    <div class="events-gallery">
      {% for img in event.images %}
        <img
          src="{{ img }}"
          alt="{{ event.name }}"
          loading="lazy">
      {% endfor %}
    </div>

  {% else %}
    <!-- NO IMAGE -->
    <div class="events-text">
      <h3>{{ event.name }}</h3>

      <p>
        <time datetime="{{ event.date | date: '%Y-%m-%d' }}">
          {{ event.date | date: "%d %B %Y" }}
        </time><br>
        {{ event.type }}<br>
        {% if event.city %}{{ event.city }}<br>{% endif %}
        Role: <strong>{{ event.role }}</strong><br>
      {% if event.platform %}
        Platform:
        {% if event.platform_url %}
          <a href="{{ event.platform_url }}">{{ event.platform }}</a>
        {% else %}
          {{ event.platform }}
        {% endif %}
      </p>
      {% endif %}
    </div>
  {% endif %}

</section>
{% endfor %}