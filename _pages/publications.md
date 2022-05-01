---
layout: page
permalink: /publications/
title: publications
description: 
years: [2022, 2021]
nav: true
---

Visit my [Google Scholar Profile](https://scholar.google.com/citations?user=b8b_eGUAAAAJ&hl=en)

<div class="publications">

{% for y in page.years %}
  <h2 class="year">{{y}}</h2>
  {% bibliography -f papers -q @*[year={{y}}]* %}
{% endfor %}

</div>
