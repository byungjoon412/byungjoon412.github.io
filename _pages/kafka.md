---
title: "ClassicCS"
layout: single
permalink: /ClassicCS/
author_profile: true
---

{% assign categories_max = 0 %}
{% for category in site.categories %}
  {% if category[1].size > categories_max %}
    {% assign categories_max = category[1].size %}
  {% endif %}
{% endfor %}

{% for i in (1..categories_max) reversed %}
  {% for category in site.categories %}
    {% if category[0] == "kafka" %}
      {% if category[1].size == i %}
            {% for post in category.last %}
              {% include archive-single.html type=page.entries_layout %}
            {% endfor %}
      {% endif %}
    {% endif %}
  {% endfor %}
{% endfor %}
