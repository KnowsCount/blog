---
title: Reflections
permalink: /reflections/
layout: page
comments: false
---

{%- for tag in site.tags -%}
  {%- if tag[0] == "reflections" -%}
    {%- for post in tag[1] -%}
      <article class="post-item" id="results-container">
        <span class="post-item-date">{{ post.date | date: "%b %d, %Y" }}</span>
        <h3 class="post-item-title">
          <a href="{{ post.url }}">{{ post.title | escape }}</a>
        </h3> 
      </article>
    {%- endfor -%}
  {%- endif -%}
{%- endfor -%}