---
layout: page
title: Lessons
permalink: /lessons/
---
{% for p in site.pages %}
    {% if p.url contains 'lessons/' %}
        {% assign url_parts = p.url | split: "/" %}
        {% assign url_size = url_parts | size %}
        {% if url_size > 2 %}
            
1. [{{ p.title }}]({{ p.url | relative_url }})

        {% endif %}
    {% endif %}
{% endfor %}

