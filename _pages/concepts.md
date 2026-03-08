---
layout: page
title: concepts
permalink: /concepts/
description: A running log of concepts I learn, organized by date.
nav: true
---

{% comment %} Collect all tags from concepts {% endcomment %}
{% assign all_tags = "" | split: "" %}
{% for concept in site.concepts %}
  {% for tag in concept.tags %}
    {% unless all_tags contains tag %}
      {% assign all_tags = all_tags | push: tag %}
    {% endunless %}
  {% endfor %}
{% endfor %}

{% comment %} View toggle {% endcomment %}
<div class="concept-controls">
  <div class="concept-view-toggle">
    <button class="view-btn active" data-view="timeline" onclick="switchView('timeline', this)">
      <i class="fas fa-stream"></i> Timeline
    </button>
    <button class="view-btn" data-view="cards" onclick="switchView('cards', this)">
      <i class="fas fa-th-large"></i> Cards
    </button>
    <button class="view-btn" data-view="list" onclick="switchView('list', this)">
      <i class="fas fa-list"></i> List
    </button>
  </div>

  {% if all_tags.size > 0 %}
  <div class="tag-filters">
    <span class="tag-pill active" data-tag="all" onclick="filterConcepts('all', this)">All</span>
    {% assign sorted_tags = all_tags | sort %}
    {% for tag in sorted_tags %}
    <span class="tag-pill" data-tag="{{ tag }}" onclick="filterConcepts('{{ tag }}', this)">{{ tag }}</span>
    {% endfor %}
  </div>
  {% endif %}
</div>

{% comment %} ====== TIMELINE VIEW ====== {% endcomment %}
<div class="concept-view concept-timeline active" id="view-timeline">
  {% assign sorted_concepts = site.concepts | sort: "date" | reverse %}
  {% assign current_date = "" %}
  {% for concept in sorted_concepts %}
    {% assign concept_date = concept.date | date: "%d-%m-%Y" %}
    {% if concept_date != current_date %}
      {% if current_date != "" %}
        </div>
      {% endif %}
      {% assign current_date = concept_date %}
      <div class="timeline-date-group">
        <div class="timeline-date">
          <span class="date-badge">{{ concept.date | date: "%d %b %Y" }}</span>
        </div>
    {% endif %}
    <div class="timeline-concept" data-tags="{{ concept.tags | join: ' ' }}">
      <div class="timeline-connector"></div>
      <div class="timeline-card">
        <h3><a href="{{ concept.url | relative_url }}">{{ concept.title }}</a></h3>
        <p class="concept-desc">{{ concept.description }}</p>
        {% if concept.tags.size > 0 %}
        <div class="post-tags">
          {% for tag in concept.tags %}
          <span class="tag-badge">{{ tag }}</span>
          {% endfor %}
        </div>
        {% endif %}
      </div>
    </div>
  {% endfor %}
  {% if current_date != "" %}
    </div>
  {% endif %}
</div>

{% comment %} ====== CARDS VIEW ====== {% endcomment %}
<div class="concept-view concept-cards" id="view-cards">
  {% for concept in sorted_concepts %}
  <div class="concept-card-item" data-tags="{{ concept.tags | join: ' ' }}">
    <div class="concept-card">
      <div class="card-date">{{ concept.date | date: "%d %b %Y" }}</div>
      <h3><a href="{{ concept.url | relative_url }}">{{ concept.title }}</a></h3>
      <p class="concept-desc">{{ concept.description }}</p>
      {% if concept.tags.size > 0 %}
      <div class="post-tags">
        {% for tag in concept.tags %}
        <span class="tag-badge">{{ tag }}</span>
        {% endfor %}
      </div>
      {% endif %}
    </div>
  </div>
  {% endfor %}
</div>

{% comment %} ====== LIST VIEW ====== {% endcomment %}
<div class="concept-view concept-list-view" id="view-list">
  <table class="concept-table">
    <thead>
      <tr>
        <th>Date</th>
        <th>Concept</th>
        <th>Tags</th>
      </tr>
    </thead>
    <tbody>
      {% for concept in sorted_concepts %}
      <tr data-tags="{{ concept.tags | join: ' ' }}">
        <td class="concept-table-date">{{ concept.date | date: "%d-%m-%Y" }}</td>
        <td><a href="{{ concept.url | relative_url }}">{{ concept.title }}</a></td>
        <td>
          {% for tag in concept.tags %}
          <span class="tag-badge">{{ tag }}</span>
          {% endfor %}
        </td>
      </tr>
      {% endfor %}
    </tbody>
  </table>
</div>

<script>
function switchView(view, el) {
  document.querySelectorAll('.view-btn').forEach(function(btn) {
    btn.classList.remove('active');
  });
  el.classList.add('active');
  document.querySelectorAll('.concept-view').forEach(function(v) {
    v.classList.remove('active');
  });
  document.getElementById('view-' + view).classList.add('active');
}

function filterConcepts(tag, el) {
  document.querySelectorAll('.tag-pill').forEach(function(pill) {
    pill.classList.remove('active');
  });
  el.classList.add('active');

  // Timeline view
  document.querySelectorAll('.timeline-concept').forEach(function(item) {
    if (tag === 'all') {
      item.style.display = '';
    } else {
      var tags = item.getAttribute('data-tags').split(' ');
      item.style.display = tags.indexOf(tag) !== -1 ? '' : 'none';
    }
  });
  // Show/hide date groups if all concepts inside are hidden
  document.querySelectorAll('.timeline-date-group').forEach(function(group) {
    var concepts = group.querySelectorAll('.timeline-concept');
    var anyVisible = false;
    concepts.forEach(function(c) {
      if (c.style.display !== 'none') anyVisible = true;
    });
    group.style.display = anyVisible ? '' : 'none';
  });

  // Cards view
  document.querySelectorAll('.concept-card-item').forEach(function(item) {
    if (tag === 'all') {
      item.style.display = '';
    } else {
      var tags = item.getAttribute('data-tags').split(' ');
      item.style.display = tags.indexOf(tag) !== -1 ? '' : 'none';
    }
  });

  // List view
  document.querySelectorAll('.concept-table tbody tr').forEach(function(row) {
    if (tag === 'all') {
      row.style.display = '';
    } else {
      var tags = row.getAttribute('data-tags').split(' ');
      row.style.display = tags.indexOf(tag) !== -1 ? '' : 'none';
    }
  });
}
</script>
