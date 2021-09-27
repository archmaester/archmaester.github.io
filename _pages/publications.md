---
layout: page
permalink: /publications/
title: publications
description: 
years: [2021, 2020, 2019, 2018]
nav: true
---

Visit my [Google Scholar Profile](https://scholar.google.com/citations?user=e765N80AAAAJ&hl=en)

<div class="publications">

{% for y in page.years %}
  <h2 class="year">{{y}}</h2>
  {% bibliography -f papers -q @*[year={{y}}]* %}
{% endfor %}

</div>
