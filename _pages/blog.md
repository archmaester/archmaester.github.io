---
layout: page
title: blog
permalink: /blog/
description:
nav: false
---

{% comment %} Collect all tags {% endcomment %}
{% assign all_tags = "" | split: "" %}
{% for post in site.posts %}
  {% for tag in post.tags %}
    {% unless all_tags contains tag %}
      {% assign all_tags = all_tags | push: tag %}
    {% endunless %}
  {% endfor %}
{% endfor %}

{% if all_tags.size > 0 %}
<div class="tag-filters">
  <span class="tag-pill active" data-tag="all" onclick="filterPosts('all', this)">All</span>
  {% for tag in all_tags %}
  <span class="tag-pill" data-tag="{{ tag }}" onclick="filterPosts('{{ tag }}', this)">{{ tag }}</span>
  {% endfor %}
</div>
{% endif %}

<ul class="post-list">
  {% for post in site.posts %}
  {% assign words = post.content | number_of_words %}
  {% assign minutes = words | divided_by: 250 %}
  {% if minutes < 1 %}{% assign minutes = 1 %}{% endif %}
  <li data-tags="{{ post.tags | join: ' ' }}">
    <h3>
      <a class="post-title" href="{{ post.url | relative_url }}">{{ post.title }}</a>
    </h3>
    <p class="post-meta">
      {{ post.date | date: "%B %-d, %Y" }}
      <span class="reading-time">&middot; {{ minutes }} min read</span>
    </p>
    {% if post.tags.size > 0 %}
    <div class="post-tags">
      {% for tag in post.tags %}
      <span class="tag-badge">{{ tag }}</span>
      {% endfor %}
    </div>
    {% endif %}
    <p class="post-description">{{ post.description }}</p>
  </li>
  {% endfor %}
</ul>

<script>
function filterPosts(tag, el) {
  document.querySelectorAll('.tag-pill').forEach(function(pill) {
    pill.classList.remove('active');
  });
  el.classList.add('active');
  document.querySelectorAll('.post-list li').forEach(function(li) {
    if (tag === 'all') {
      li.style.display = '';
    } else {
      var tags = li.getAttribute('data-tags').split(' ');
      li.style.display = tags.indexOf(tag) !== -1 ? '' : 'none';
    }
  });
}
</script>
