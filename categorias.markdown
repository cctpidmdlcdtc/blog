---
layout: default
title: Categorías
permalink: /categorias/
---

<h1>Todas las categorías</h1>
<ul>
  {% for category in site.categories %}
    <li>
      <a href="{{ site.baseurl }}/categorias/{{ category[0] | slugify }}/">
        {{ category[0] }} ({{ category[1].size }})
      </a>
    </li>
  {% endfor %}
</ul>
