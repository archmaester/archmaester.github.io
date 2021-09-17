---
layout: page
title: team
permalink: /team/
social: true
nav: true
---

<hr class="navbar">
##### **PostDoc researchers**
<div class="current">

{% for member in site.currentTeam %}
  {% if member.type == "PostDoc" %}
  	{% if member.html %}
  	<p><a href="{{ member.html }}">{{ member.name }}</a>, {{ member.join}} - {{member.leave}} (Prev Affiliation: {{ member.prev }})</p>
    {% else %}
  	<p>{{ member.name }}, {{ member.join}} - {{member.leave}} (Prev Affiliation: {{ member.prev }})</p>
  	{% endif %}
  {% endif %}
{% endfor %}

</div>

<hr class="navbar">
##### **PhD students**
<div class="current">

{% for member in site.currentTeam %}
  {% if member.type == "PhD" %}
  	{% if member.html %}
  	<p><a href="{{ member.html }}">{{ member.name }}</a>, {{ member.join}} - {{member.leave}} (Prev Affiliation: {{ member.prev }})</p>
    {% else %}
  	<p>{{ member.name }}, {{ member.join}} - {{member.leave}} (Prev Affiliation: {{ member.prev }})</p>
  	{% endif %}
  {% endif %}
{% endfor %}

</div>

<hr class="navbar">
##### **Masters students**
<div class="current">

{% for member in site.currentTeam %}
  {% if member.type == "Masters" %}
  	{% if member.html %}
  	<p><a href="{{ member.html }}">{{ member.name }}</a>, {{ member.join}} - {{member.leave}} (Prev Affiliation: {{ member.prev }})</p>
    {% else %}
  	<p>{{ member.name }}, {{ member.join}} - {{member.leave}} (Prev Affiliation: {{ member.prev }})</p>
  	{% endif %}
  {% endif %}
{% endfor %}

</div>

<hr class="navbar">
##### **Interns**
<div class="current">

{% for member in site.currentTeam %}
  {% if member.type == "Intern" %}
	{% if member.html %}
  	<p><a href="{{ member.html }}">{{ member.name }}</a>, {{ member.join}} - {{member.leave}} (Prev Affiliation: {{ member.prev }})</p>
    {% else if member.url %}
  	<p><a href="{{ member.url }}">{{ member.name }}</a>, {{ member.join}} - {{member.leave}} (Prev Affiliation: {{ member.prev }})</p>
  	{% else %}
  	<p>{{ member.name }}, {{ member.join}} - {{member.leave}} (Prev Affiliation: {{ member.prev }})</p>
  	{% endif %}
  {% endif %}
{% endfor %}

</div>

<hr class="navbar">
##### **Alumni**
<div class="alumni">

{% for member in site.currentTeam %}
  {% if member.Alumni %}
  	{% if member.html %}
  	<p><a href="{{ member.html }}">{{ member.name }}</a>, {{ member.join}} - {{member.leave}} (Next: {{ member.future }})</p>
    {% else %}
  	<p>{{ member.name }}, {{ member.join}} - {{member.leave}} (Next: {{ member.future }})</p>
  	{% endif %}
  {% endif %}
{% endfor %}

</div>

<!-- Write your biography here. Tell the world about yourself. Link to your favorite [subreddit](http://reddit.com){:target="\_blank"}. You can put a picture in, too. The code is already in, just name your picture `prof_pic.jpg` and put it in the `img/` folder.

Put your address / P.O. box / other info right below your picture. You can also disable any these elements by editing `profile` property of the YAML header of your `_pages/about.md`. Edit `_bibliography/papers.bib` and Jekyll will render your [publications page](/al-folio/publications/) automatically.

Link to your social media connections, too. This theme is set up to use [Font Awesome icons](http://fortawesome.github.io/Font-Awesome/){:target="\_blank"} and [Academicons](https://jpswalsh.github.io/academicons/){:target="\_blank"}, like the ones below. Add your Facebook, Twitter, LinkedIn, Google Scholar, or just disable all of them. -->
