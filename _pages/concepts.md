---
layout: page
title: concepts
permalink: /concepts/
description: A running log of concepts I learn, organized by date.
nav: false
---

{% comment %} Collect all tags, sources, statuses from concepts {% endcomment %}
{% assign all_tags = "" | split: "" %}
{% assign all_sources = "" | split: "" %}
{% assign all_statuses = "" | split: "" %}
{% for concept in site.concepts %}
  {% for tag in concept.tags %}
    {% unless all_tags contains tag %}
      {% assign all_tags = all_tags | push: tag %}
    {% endunless %}
  {% endfor %}
  {% if concept.source %}
    {% unless all_sources contains concept.source %}
      {% assign all_sources = all_sources | push: concept.source %}
    {% endunless %}
  {% endif %}
  {% if concept.status %}
    {% unless all_statuses contains concept.status %}
      {% assign all_statuses = all_statuses | push: concept.status %}
    {% endunless %}
  {% endif %}
{% endfor %}

{% comment %} Count concepts needing review (not reviewed in 30+ days) {% endcomment %}
{% assign needs_review_count = 0 %}
{% assign today = "now" | date: "%s" %}
{% for concept in site.concepts %}
  {% if concept.last_reviewed %}
    {% assign reviewed = concept.last_reviewed | date: "%s" %}
    {% assign diff = today | minus: reviewed %}
    {% if diff > 2592000 %}
      {% assign needs_review_count = needs_review_count | plus: 1 %}
    {% endif %}
  {% endif %}
  {% unless concept.last_reviewed %}
    {% assign needs_review_count = needs_review_count | plus: 1 %}
  {% endunless %}
{% endfor %}

{% comment %} View toggle {% endcomment %}
<div class="concept-controls">
  <div class="concept-view-toggle">
    <button class="view-btn" data-view="timeline" onclick="switchView('timeline', this)">
      <i class="fas fa-stream"></i> Timeline
    </button>
    <button class="view-btn active" data-view="cards" onclick="switchView('cards', this)">
      <i class="fas fa-th-large"></i> Cards
    </button>
    <button class="view-btn" data-view="list" onclick="switchView('list', this)">
      <i class="fas fa-list"></i> List
    </button>
  </div>

  {% if all_tags.size > 0 %}
  <div class="tag-filters">
    <span class="filter-label">Tags:</span>
    <span class="tag-pill active" data-tag="all" onclick="filterConcepts('all', this)">All</span>
    {% assign sorted_tags = all_tags | sort %}
    {% for tag in sorted_tags %}
    <span class="tag-pill" data-tag="{{ tag }}" onclick="filterConcepts('{{ tag }}', this)">{{ tag }}</span>
    {% endfor %}
  </div>
  {% endif %}

  {% if all_statuses.size > 0 %}
  <div class="tag-filters status-filters">
    <span class="filter-label">Status:</span>
    <span class="tag-pill active" data-status="all" onclick="filterByStatus('all', this)">All</span>
    {% for s in all_statuses %}
    <span class="tag-pill status-{{ s }}" data-status="{{ s }}" onclick="filterByStatus('{{ s }}', this)">{{ s }}</span>
    {% endfor %}
    {% if needs_review_count > 0 %}
    <span class="tag-pill needs-review-pill" data-status="needs-review" onclick="filterByStatus('needs-review', this)">Needs Review ({{ needs_review_count }})</span>
    {% endif %}
  </div>
  {% endif %}
</div>

{% comment %} ====== TIMELINE VIEW ====== {% endcomment %}
<div class="concept-view concept-timeline" id="view-timeline">
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
    {% assign tl_review_stale = false %}
    {% if concept.last_reviewed %}
      {% assign tl_reviewed_s = concept.last_reviewed | date: "%s" %}
      {% assign tl_diff_s = today | minus: tl_reviewed_s %}
      {% if tl_diff_s > 2592000 %}{% assign tl_review_stale = true %}{% endif %}
    {% else %}
      {% assign tl_review_stale = true %}
    {% endif %}
    <div class="timeline-concept" data-tags="{{ concept.tags | join: ' ' }}" data-concept="{{ concept.slug }}" data-status="{{ concept.status | default: 'seed' }}" data-needs-review="{{ tl_review_stale }}">
      <div class="timeline-connector"></div>
      <div class="timeline-card" onclick="toggleConcept('{{ concept.slug }}', this)">
        <h3>{{ concept.title }}</h3>
        <div class="card-meta-badges">
          {% if concept.status %}<span class="status-badge status-{{ concept.status }}">{{ concept.status }}</span>{% endif %}
          {% if concept.source %}<span class="source-badge">{{ concept.source }}</span>{% endif %}
        </div>
        {% if concept.tags.size > 0 %}
        <div class="post-tags">
          {% for tag in concept.tags %}
          <span class="tag-badge">{{ tag }}</span>
          {% endfor %}
        </div>
        {% endif %}
      </div>
      <div class="concept-detail" id="detail-timeline-{{ concept.slug }}">
        <div class="concept-detail-inner">
          {% if concept.paper %}
          <p class="concept-paper"><strong>Paper:</strong> <a href="{{ concept.paper.url }}">{{ concept.paper.authors }} ({{ concept.paper.year }})</a></p>
          {% endif %}
          {{ concept.content }}
        </div>
      </div>
    </div>
  {% endfor %}
  {% if current_date != "" %}
    </div>
  {% endif %}
</div>

{% comment %} ====== CARDS VIEW ====== {% endcomment %}
<div class="concept-view concept-cards active" id="view-cards">
  {% for concept in sorted_concepts %}
  {% assign review_stale = false %}
  {% if concept.last_reviewed %}
    {% assign reviewed_s = concept.last_reviewed | date: "%s" %}
    {% assign diff_s = today | minus: reviewed_s %}
    {% if diff_s > 2592000 %}{% assign review_stale = true %}{% endif %}
  {% else %}
    {% assign review_stale = true %}
  {% endif %}
  <div class="concept-card-item" data-tags="{{ concept.tags | join: ' ' }}" data-concept="{{ concept.slug }}" data-status="{{ concept.status | default: 'seed' }}" data-needs-review="{{ review_stale }}">
    <div class="concept-card" onclick="toggleConcept('{{ concept.slug }}', this)">
      <div class="card-header-meta">
        <div class="card-date">{{ concept.date | date: "%d %b %Y" }}</div>
        <div class="card-meta-badges">
          {% if concept.status %}<span class="status-badge status-{{ concept.status }}">{{ concept.status }}</span>{% endif %}
          {% if concept.source %}<span class="source-badge">{{ concept.source }}</span>{% endif %}
        </div>
      </div>
      <h3>{{ concept.title }}</h3>
      {% if concept.tags.size > 0 %}
      <div class="post-tags">
        {% for tag in concept.tags %}
        <span class="tag-badge">{{ tag }}</span>
        {% endfor %}
      </div>
      {% endif %}
      {% if concept.last_reviewed %}
      <div class="card-reviewed {% if review_stale %}stale{% endif %}">
        Reviewed: {{ concept.last_reviewed | date: "%d %b %Y" }}{% if review_stale %} — stale{% endif %}
      </div>
      {% endif %}
    </div>
    <div class="concept-detail" id="detail-cards-{{ concept.slug }}">
      <div class="concept-detail-inner">
        {% if concept.paper %}
        <p class="concept-paper"><strong>Paper:</strong> <a href="{{ concept.paper.url }}">{{ concept.paper.authors }} ({{ concept.paper.year }})</a></p>
        {% endif %}
        {{ concept.content }}
      </div>
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
        <th>Status</th>
        <th>Source</th>
        <th>Tags</th>
      </tr>
    </thead>
    <tbody>
      {% for concept in sorted_concepts %}
      {% assign lst_review_stale = false %}
      {% if concept.last_reviewed %}
        {% assign lst_reviewed_s = concept.last_reviewed | date: "%s" %}
        {% assign lst_diff_s = today | minus: lst_reviewed_s %}
        {% if lst_diff_s > 2592000 %}{% assign lst_review_stale = true %}{% endif %}
      {% else %}
        {% assign lst_review_stale = true %}
      {% endif %}
      <tr data-tags="{{ concept.tags | join: ' ' }}" data-concept="{{ concept.slug }}" data-status="{{ concept.status | default: 'seed' }}" data-needs-review="{{ lst_review_stale }}" onclick="toggleConcept('{{ concept.slug }}', this)" style="cursor: pointer;">
        <td class="concept-table-date">{{ concept.date | date: "%d-%m-%Y" }}</td>
        <td>{{ concept.title }}</td>
        <td><span class="status-badge status-{{ concept.status | default: 'seed' }}">{{ concept.status | default: "seed" }}</span></td>
        <td>{{ concept.source | default: "—" }}</td>
        <td>
          {% for tag in concept.tags %}
          <span class="tag-badge">{{ tag }}</span>
          {% endfor %}
        </td>
      </tr>
      <tr class="concept-detail-row" id="detail-list-{{ concept.slug }}" style="display: none;">
        <td colspan="5">
          <div class="concept-detail-inner">
            {% if concept.paper %}
            <p class="concept-paper"><strong>Paper:</strong> <a href="{{ concept.paper.url }}">{{ concept.paper.authors }} ({{ concept.paper.year }})</a></p>
            {% endif %}
            {{ concept.content }}
          </div>
        </td>
      </tr>
      {% endfor %}
    </tbody>
  </table>
</div>

<script>
function switchView(view, el) {
  // Close all open details first
  document.querySelectorAll('.concept-detail').forEach(function(d) {
    d.classList.remove('open');
  });
  document.querySelectorAll('.concept-detail-row').forEach(function(r) {
    r.style.display = 'none';
  });

  document.querySelectorAll('.view-btn').forEach(function(btn) {
    btn.classList.remove('active');
  });
  el.classList.add('active');
  document.querySelectorAll('.concept-view').forEach(function(v) {
    v.classList.remove('active');
  });
  document.getElementById('view-' + view).classList.add('active');
}

function toggleConcept(slug, el) {
  // Determine which view is active
  var views = ['timeline', 'cards', 'list'];
  views.forEach(function(view) {
    var detail = document.getElementById('detail-' + view + '-' + slug);
    if (!detail) return;
    var viewContainer = document.getElementById('view-' + view);
    if (!viewContainer || !viewContainer.classList.contains('active')) return;

    if (view === 'list') {
      // Toggle table row visibility
      detail.style.display = detail.style.display === 'none' ? '' : 'none';
    } else {
      detail.classList.toggle('open');
    }
  });
}

function filterByStatus(status, el) {
  document.querySelectorAll('.status-filters .tag-pill').forEach(function(pill) {
    pill.classList.remove('active');
  });
  el.classList.add('active');

  // Close all open details
  document.querySelectorAll('.concept-detail').forEach(function(d) {
    d.classList.remove('open');
  });
  document.querySelectorAll('.concept-detail-row').forEach(function(r) {
    r.style.display = 'none';
  });

  var selectors = ['.timeline-concept', '.concept-card-item', '.concept-table tbody tr[data-concept]'];
  selectors.forEach(function(sel) {
    document.querySelectorAll(sel).forEach(function(item) {
      if (status === 'all') {
        item.style.display = '';
      } else if (status === 'needs-review') {
        item.style.display = item.getAttribute('data-needs-review') === 'true' ? '' : 'none';
      } else {
        item.style.display = item.getAttribute('data-status') === status ? '' : 'none';
      }
    });
  });

  // Hide empty timeline date groups
  document.querySelectorAll('.timeline-date-group').forEach(function(group) {
    var concepts = group.querySelectorAll('.timeline-concept');
    var anyVisible = false;
    concepts.forEach(function(c) {
      if (c.style.display !== 'none') anyVisible = true;
    });
    group.style.display = anyVisible ? '' : 'none';
  });
}

function filterConcepts(tag, el) {
  document.querySelectorAll('.tag-pill').forEach(function(pill) {
    pill.classList.remove('active');
  });
  el.classList.add('active');

  // Close all open details
  document.querySelectorAll('.concept-detail').forEach(function(d) {
    d.classList.remove('open');
  });
  document.querySelectorAll('.concept-detail-row').forEach(function(r) {
    r.style.display = 'none';
  });

  // Timeline view
  document.querySelectorAll('.timeline-concept').forEach(function(item) {
    if (tag === 'all') {
      item.style.display = '';
    } else {
      var tags = item.getAttribute('data-tags').split(' ');
      item.style.display = tags.indexOf(tag) !== -1 ? '' : 'none';
    }
  });
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
  document.querySelectorAll('.concept-table tbody tr[data-concept]').forEach(function(row) {
    if (tag === 'all') {
      row.style.display = '';
    } else {
      var tags = row.getAttribute('data-tags').split(' ');
      row.style.display = tags.indexOf(tag) !== -1 ? '' : 'none';
    }
  });
}
</script>
